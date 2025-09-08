---
title: Elixir code coverage with partition test
description: >-
  With the release of Elixir 1.10, the --partition option was added to the mix
  test to be able to split our test suit in runners.
date: '2023-05-05T11:15:56.023Z'
categories: []
keywords: []
slug: /coverage-with-partition-test-a7835a147b42
cover:
  image: /images/1__sot8WkLZk15LmPF1YghZPg.jpeg
---

With the [release](https://github.com/elixir-lang/elixir/releases/tag/v1.10.0) of Elixir 1.10, the `--partition` option was added to the mix test to be able to split our test suit in runners.

For this use case, I’ve used [ex_coveralls](https://github.com/parroty/excoveralls) to generate the coverage report. To get the coverage for our test suite we need to export the `.coverdata` files for each runner, using `--export-coverage` option that is available since version 0.15.2

When [running with partitions](https://hexdocs.pm/mix/Mix.Tasks.Test.html#module-command-line-options) you need to define `MIX_TEST_PARTITION` environment variable with a value between 1 and 4.

```bash
# Repeat for 1 to 4  
export MIX_TEST_PARTITION=1  
mix coveralls --partitions 4 --export-coverage "run_$MIX_TEST_PARTITION"
```


After each test run you will have 4 different `.coverdata` files inside the `cover` folder: `run_1.coverdata, run_2.coverdata, run_3.coverdata, run_4.coverdata` .

### Aggregate cover data

To generate a report with these files there are two options, a simple one and a more difficult one.

#### Simple one

We can aggregate and export these metrics by running any kind of test. Since [ExCoveralls 0.15.3](https://github.com/parroty/excoveralls/blob/master/CHANGELOG.md) we can import coverage data from other tests using `--import_cover <folder>` (normally the folder would be `cover` ).

Since we don’t want to run any particular test again we can use the following command to not run any test but generate the coverage report.
```bash
mix coveralls --no-start --exclude test --import-cover cover
```

If you are just interested in the global coverage, you can extract it from `coveralls.html` using the following bash command:
```bash
export COVERAGE=$(cat cover/excoveralls.html | grep “percentage” | sed -n ‘2 p’ | grep -E -o ‘\[0–9.\]{1,5}’)
```

#### Difficult one

If for some reason you don’t want to set up everything to run `mix coveralls` there is another option, but keep in mind that you will need the `.beam` files, normally stored in `_build/test/lib/<project_name>/ebin` .

The following Elixir code enables you to combine all the `.coverdata` files in one file. It would be needed for the next command.


```bash
# Import coverdata files  
File.ls!("cover")  
|> Enum.filter(&(String.contains?(&1, ".coverdata")))  
|> Enum.each(fn file ->   
  "cover/#{file}"   
  |> String.to_charlist()   
  |> :cover.import()   
end)  
  
# Debug  
:cover.imported()   
|> IO.inspect()  
  
# Export to one file  
:cover.export('cover/all.coverdata')
```

Then we can generate the coverage report with [Covertool](https://github.com/covertool/covertool). If you have [issues compiling](https://github.com/covertool/covertool/issues/44) the code, you can use`REBAR="" make deps; make compile`. Make sure you have OTP 25 installed.

```bash
./covertool -cover cover/all.coverdata -ebin _build/test/lib/<project_name>/ebin
```

This will generate a `coverage.xml` file with the coverage information. If you want to extract the global coverage you can use the following commands.

```bash
# Extract line-rate value from file  
export COVERAGE_FLOAT=$(xq -r ‘.coverage.”@line-rate”’ coverage.xml)  

# Calculate percentage  
awk -vcoverage=$COVERAGE_FLOAT ‘BEGIN{print coverage*100}’
```


### Conclusion

I hope you find this useful to you. Let me know if you had issues making this work on your project.

