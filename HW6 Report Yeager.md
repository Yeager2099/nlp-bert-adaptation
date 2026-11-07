# Report transcription

Text extracted from the author-provided course report. Figures are not reproduced and tables may lose formatting. Student identifiers and institutional email addresses are removed.

## Page 1

CIS 5300: Natural Language Processing
Homework 6: BERT & Contextual Embeddings
Report Template
Name: Yeager Ye
Penn ID: [STUDENT_ID_REDACTED]
Learning Objectives
1. Use pretrained BERT zero-shot via mask-filling and understand its limitations
without fine-tuning.
2. Fine-tune BERT for sequence classification using the `[CLS]` token for
sentiment analysis.
3. Fine-tune BERT for sentence-pair tasks using `[SEP]` concatenation for natural
language inference.
4. Fine-tune BERT for token classification with BIO tagging and subword-to-word
label alignment for named entity recognition.
5. Evaluate NER models using macro-averaged F1 over entity types.
6. Analyze transfer learning by ablating pre-training and comparing zero-shot vs.
fine-tuned performance.
7. Navigate the HuggingFace ecosystem: `transformers`, `datasets`, and
`Trainer`.

## Page 2

CIS 5300: Natural Language Processing
Points Breakdown
Section Points Description
Answer 5.1 5 pts Zero-Shot vs Fine-Tuned Comparison
Answer 5.2 4 pts Pre-Training Ablation Analysis
Total 9 pts
Answer 5.1: Zero-Shot vs Fine-Tuned Comparison
Pick 10 specific Yelp reviews from the evaluation set. For each, report:
- The true star rating (1–5)
- Your zero-shot prediction (Section 1 mask-filling)
- Your fine-tuned prediction (Section 2 classifier)
Then answer: on which examples does zero-shot already work? On which does fine-
tuning help most? What types of reviews does fine-tuning fix?
Review (first 50 chars) True label Zero-shot Fine-tuned
The food here is absolutely 5 3 5
amaz
Terrible service, waited an hour 1 3 1
Pretty decent, nothing too 3 3 3
specia
I wouldn’t say it was bad but it 2 3 2
Went for lunch on a Tuesday. 4 3 4
The

## Page 3

CIS 5300: Natural Language Processing
Extremely disappointing. Cold 1 1 1
foo
My second visit here was even 5 3 5
bet
Mediocre at best. The burger 2 3 2
was
Best brunch spot I’ve tried in th 5 5 5
Went in expecting great things 2 3 2
bu
Zero-shot BERT performs reasonably well on strongly polar reviews. In the evaluation
sample, examples 6 and 9 (true labels 1 and 5) received correct zero-shot predictions,
presumably because words such as "Extremely disappointing" and "Best brunch spot"
are unambiguous signals. Fine-tuning matters most for nuanced, mid-range reviews.
Examples 4 and 10 have true label 2, but the zero-shot model assigned both a score
of 3; the mask-filling approach appears to map mixed or moderately negative
language to the neutral digit by default. After fine-tuning, the classifier correctly
predicts 2 for both. Example 7 (true label 5) begins with seemingly neutral context
("My second visit"), causing the zero-shot model to predict 3; the fine-tuned model
predicts 5. In general, fine-tuning improves performance on reviews whose sentiment
is distributed across sentences rather than concentrated at the beginning, and on
reviews that rely on subtle comparative phrasing such as "even better than last time"
or "nothing too special".

## Page 4

CIS 5300: Natural Language Processing
Answer 5.2: Pre-Training Ablation Analysis
You trained the same BERT architecture twice: once starting from pre-trained
weights, once from random initialization. Compare the results.
1.How much does pre-training improve accuracy over random initialization? Report
the numbers.
2. Why is the gap so large with only 1,000 training examples? What knowledge
does pre-training provide that can't be learned from 1000 labeled reviews?
3. Would you expect the gap to shrink if you had 100,000 training examples? Why
or why not?
Model Accuracy
Pre-trained BERT (fine-tuned) 59.3%
Random Initialization 22.0%
1. Pre-trained BERT achieved 59.3% accuracy, while the randomly initialized model
achieved 22.0%. The gap of 37.3 percentage points is substantial. A randomly
initialized model on a 5-class problem has a 20% random baseline. That the random-
init BERT accuracy is close to this baseline confirms that essentially no useful
representations were learned from scratch with only 1,000 examples.
2. The gap is large because BERT has 110 million parameters and cannot be reliably
trained on 1,000 labeled examples alone; it would severely overfit or fail to converge.
Pre-trained BERT already encodes general-purpose linguistic knowledge
(morphology, syntax, word senses, coreference, pragmatics) acquired from billions of
tokens of diverse text. That knowledge cannot be replicated from a 1,000-example
sentiment corpus. Fine-tuning only needs to learn how to map the pre-trained
sentence representation onto five classes, which requires far fewer labeled examples
than learning representations from random initialization.

## Page 5

CIS 5300: Natural Language Processing
3. The gap would narrow with 100,000 training examples but would not fully close.
Given enough labeled data, a randomly initialized model can in principle learn
competent task-specific representations. Pre-trained BERT would nonetheless retain
an advantage: it converges faster, handles rare vocabulary and unusual phrasing more
robustly, and draws on linguistic knowledge (syntax, world knowledge) that
sentiment labels alone cannot supply. Studies of pre-training (e.g., ULMFiT, GPT)
show that pre-trained models require roughly 10 to 100 times less labeled data to
reach the same accuracy as a randomly initialized model. The 37-point gap observed
at 1,000 examples would therefore be expected to shrink to a few points at 100,000
examples.
