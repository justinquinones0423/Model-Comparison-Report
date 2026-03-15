# Model Comparison Report — Week 4

**Name:** Justin Quinones
**Date:** 3/14/2025
**Capstone Project:** Security Alert Triage Bot
**My Component:** Ingestion

## Test Setup

**Input dataset:** 5 cybersecurity text samples covering:
- 2 clearly concerning/high-severity records
- 1 ambiguous/edge case record
- 2 routine/benign records

**Models tested:**
1. distilbert-base-uncased-finetuned-sst-2-english (sentiment)
2. facebook/bart-large-mnli (zero-shot classification)
3. dslim/bert-large-NER (named entity recognition)
4. Groq Llama 3 8B (LLM classification)

**Evaluation criteria:** label accuracy, confidence score, speed, ease of
integration in n8n

## Results Summary

| Record | Sentiment | Zero-Shot | NER Entities | Groq |
|--------|-----------|-----------|-------------|------|
| 1 | NEGATIVE/0.9956 | possible anomaly | {"entity_group":"LOC","score":0.99956995,"word":"Moscow","start":65,"end":71} | CRITICAL |
| 2 | NEGATIVE/0.9986 | critical threat  | {"entity_group":"ORG","score":0.7415041,"word":"Cloudflare","start":63,"end":73} | INFORMATIONAL |
| 3 | NEGATIVE/0.9958 | possible anomaly | {"entity_group":"LOC","score":0.98019344,"word":"Beijing","start":49,"end":56} | HIGH |
| 4 | NEGATIVE/0.9996 | critical threat  | {"entity_group":"PER","score":0.9980019,"word":"John Miller","start":82,"end":93} | HIGH |
| 5 | NEGATIVE/0.9962 | routine activity | {"entity_group":"MISC","score":0.98080975,"word":"SS","start":31,"end":33} | INFORMATIONAL |

## Analysis

**Where models agreed:** The record that the models classified the same way were record 5. Model 2 labeled is as a possible anomaly and model 4 labeled it as informational.

**Where models disagreed:** The records that got different classifications were 2 and 4. For the 2nd record, model 1 labeled it negative along with a confidence score of 0.9986 which is wrong. Model 2 labeled it as a critical threat with a 0.1 which is the wrong label. Only the 4th model got the classification correct.
As for the 4th record, model 2 also labeled it as a critical threat but gave it a low score which should have been higher. The 4th model was able to give it the right label.

**Most accurate model overall:** Based on the 5 records, the model that gave the most sensible results was the Groq model as it correctly labeled all records. 

**Fastest/most practical:** The model that would be easiest to use at a scale would be the Sentiment model.

## Recommended Models for My Capstone Component

**Component:** Ingestion

**Primary model:** Model 4 Groq — Every Groq classification came with an explanation which is highly useful because one would need to understand why something was escalated, not just that it was.

**Secondary model (if applicable):** Model 2 Zero Shot — It can pre-filter at scale making it ideal as a first-pass filter before Groq processes an alert.

**Rejected models and why:**
- Model 1 Sentiment and Model 3 NER Entities: Model 1 wouldn't be a right fit because it just flags everything as negative which would not be useful at all. Model 3 on the other hand would not be as useful since it does not have a proper classification ability.

## Failure Cases and Limitations

One case where a model gave a wrong result would be model 2 where it labels an SSH Brute-Force Attack as critical threat but with Only 0.1 Confidence. 
This is the most dangerous kind of wrong result and not because the label is incorrect, but because the confidence score is completely contradictory. A brute-force attack with 47 attempts in 5 minutes is about as unambiguous a threat signal as you can get, yet Model 2 returned its lowest possible confidence score on it.
What this tells me about production use is that it would require constant human validation and it's a liability at scale.

## Next Steps

What I would test text if I had more time would be more records so I could have at least up to ten so that it can have more and I would also test a different model as well to see a different classification. 
