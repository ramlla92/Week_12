# Gap Closure Assessment

Status: **Closed**

Before exploring this question, I assumed that the quality of the “Chosen” responses in my ORPO dataset was the primary factor influencing post-training behavior. I did not fully consider how the semantic quality and difficulty of the “Rejected” responses could shape what the model actually learns during preference optimization.

Through this investigation, I now understand that if rejected samples are too obviously poor or generic, the model may only learn to avoid low-quality outputs rather than learn the subtle distinctions between “generic” and “high-quality personalized” SDR outreach. I also learned why “Near-Miss” rejected samples  outputs that are almost correct but fail one important personalization or grounding constraint  can create stronger learning signals and sharper calibration during ORPO training.

Most importantly, I now understand that preference-pair construction directly affects post-training behavior, personalization quality, and calibration. The semantic distance between Chosen and Rejected outputs is not just a dataset detail  it is part of the learning signal itself.