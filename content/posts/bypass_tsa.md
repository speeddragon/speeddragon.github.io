---
date: "2024-02-13T16:09:42+01:00"
draft: false
title: Jump the TSA queue at the airport
---
On a recent trip to the United States, I noticed there is a way to skip the TSA queue waiting for clearing immigration going out of the country, so I decided to share it here. But first I want to tell you how I ended up finding it.

I arrived at the airport and went to the TSA queue for my designated flight gate. Waited until it was my time to go through with my passport and online boarding pass. While my friend went without any issues, I didn’t have the same result. The TSA officer told me to go back to the airline check-in counter and ask them to reprint my boarding ticket. He also gave me this little paper.


![VID Check](/images/vid_check.webp#center)

<p class="image-description">
Paper with the TSA request (sorry for the finger, added the missing words)
</p>

Although I cannot be 100% sure, I think this happened because the name on my boarding pass is different from the one on my passport since the airline has a character limit and my full name didn’t fit, hence the need for an identity validation from the airline person.

Also, while doing the online check-in on the way to the USA, I couldn’t get the boarding pass (although my friend who was under the same reservation could), not sure if the problem was related or not. I tried to ask at the airline counter but they didn’t know the reason and only printed my boarding pass.

Back to the VID check, the ticket explained that my identity needed to be validated by the airline and that a new boarding pass needed to be issued. **Also states that to return to the TSA front line.** I’m not sure if all TSA counters and queues (there were several in the airport I was in) had this “fast lane” queue, but on the airline check-in, they directed me to a specific one that had a lot of people waiting in the normal queues.

After going through the airline counter and getting my physical boarding pass (which they stated my friend also needed to get to board the airplane), I went to the TSA agent organizing the people at the end of the queue that allowed me to get into the fast lane with only 2 people ahead of me and the rest of the process was normal.

And that was it. But since I’m geeky about this stuff, I wanted to know what is the difference between the information on the online boarding pass and the physical one. Both have the PDF417 bar code with the same information except the 2 fields, one with some small changes that I cannot understand their meaning and another with some kind of signature hash (probably base64).

![Boarding Pass](/images/boarding_pass.webp#center)
<p style="text-align: center; opacity: 80%;">
Photo by <a href="https://unsplash.com/@cardmapr?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">CardMapr.nl</a> on <a href="https://unsplash.com/photos/a-passport-and-a-boarding-pass-are-on-a-bag-LVA3S6isNYQ?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>
</p>

The first field had the values `O4041B` (boarding pass without signature) and `W4040B` (boarding pass with signature) and the field with the following signature.

```
^460MEQCIBQjSWpN+TEZFiBjXn4Pllg1U/lVGhDGnuTwYr3a3DavAiA5MFJ+yDBOOKvwIEt+swe2L1oPRtI44hzkbLqKBuVW3A==
```
```
```

The signature field should be signed with the airline's private key, but couldn’t find any information regarding this except [this website](https://www.iata.org/contentassets/1dccc9ed041b4f3bbdcf8ee8682e75c4/2021_03_02-bcbp-implementation-guide-version-7-.pdf).

EDIT: Reading similar cases online like [this](https://www.reddit.com/r/tsa/comments/14251oh/cat_vid_and_not_the_cute_funny_kind_or_is/), looks like additional information is sent by the airline to the airport system to let me go through.
