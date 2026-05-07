# Day 3
URL: https://x.com/akmel3784/status/2052385563395736034?s=20

1/ In preference-tuned classifiers, high accuracy does not always mean the model learned real reasoning.

Sometimes the model simply learns explanation patterns associated with specific verdict labels.

---

2/ During training, loss is applied across every generated token:
- the verdict
- and the explanation

This means the model may optimize for explanation style instead of the actual decision boundary.

---

3/ If “Pass” examples consistently use grounded/confident wording while “Fail” examples use corrective language, the model can learn shortcut correlations instead of true classification logic.

---

4/ To test real reasoning behavior:
- vary explanation styles
- use counterfactual examples
- add hard negative samples
- evaluate on unseen reasoning formats

Otherwise, the model may only memorize patterns rather than learn decision-making logic.