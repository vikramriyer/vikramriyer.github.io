---
title: Fastbook (FastAI) Chapter 10 Questionnaire
date: 2020-10-12 19:30:50
comments: true
share: true
related: true
excerpt: NLP Deep Dive
categories: machine_learning
---

## QnA

### Q1. What is "self-supervised learning"?

__A.__

It is a kind of learning paradigm which does not require labels. Part of the independent data is used as the label.

### Q2. What is a "language model"?

__A.__

Given a sequence of text data, the language model predicts the next word in the sequence. An example, though not the best, can be, google predicting the next word(s) based on what we are typing at runtime. The reason this might not be a good example is because, this task can be done efficiently by using some other techniques that might not involve using Machine Learning. It helps get the point across nevertheless.

### Q3. Why is a language model considered self-supervised?

__A.__

There are no labels provided and the independent data is itself used to construct the dependent data. The process of getting labels is automatic and hence the term self-supervised.

### Q4. What are self-supervised models usually used for?

__A.__

Used for pre-training existing models to work in case of transfer learning. It is usually not used to train directly.

### Q5. Why do we fine-tune language models?

__A.__

To begin with there are 2 ways we can fine-tune:-
1. Fine-tune the last few layers to match the problem i.e. use a language pre-trained model, unfreeze the last few layers and modify to get results for a classifier
2. Fine-tune the entire model. This actually updates the weights where vocab of our task at hand matches the pre-trained model vocab. (__Imp__: This is not as expensive as training from scratch, as there is minor update to the words in new data if it has occurred in old language model, whereas we initialise with a random weight where the words/tokens are new)

The 2nd point is better explained with an example.

Suppose we have a pre-trained model that has been trained on wikipedia data. Now, let's say we have problem where we are expected to guess if a youtube comment is toxic or not. The problem with using the pre-trained model directly is that wikipedia data might be more formal in nature as compared to comments in youtube. So, if fine-tune the language model before directly using it for classification, we might get better results. The fine-tuning will help change the weights where formal language is replaced with informal language.

In short, we are not only fine-tuning the classification model, but also the pre-trained weights of the language model. This method is called ULMFiT (Universal Language Model Fine-tuning).

Quote from the book:-
> The paper introducing it showed that this extra stage of fine-tuning the language model, prior to transfer learning to a classification task, resulted in significantly better predictions.

### Q6. What are the three steps to create a state-of-the-art text classifier?

__A.__

_Step 1_: Choose a pre-trained language model (say Wikitext 103)
_Step 2_: Fine tune the language model to the problem at hand (say Youtube comment classification)
_Step 3_: Fine-tuning the classifier

### Q7. How do the 50,000 unlabeled movie reviews help us create a better text classifier for the IMDb dataset?

__A.__

As discussed in "#A5", along with the 50k labelled movie reviews, the 50k unlabelled reviews can be used to fine-tune the pre-trained wikitext language model. So, the new language model will have details about the names of the actors, movie names, an informal language style. Now, the classification model will work even better with such a model that has embedded the style of the problem domain i.e. IMDb comment classification.

### Q8. What are the three steps to prepare your data for a language model?

__A.__

- Tokenization
- Numericalization
- Batching data after resizing (padding) to fit the same length OR Language Model data loader creation

### Q9. What is "tokenization"? Why do we need it?

__A.__

Converting the entire text into list of words.

### Q10. Name three different approaches to tokenization.

__A.__

1. Word-based <br>
Split a sentence on spaces and additional language specific rules. For example, "don't" => "do n't".

2. Subword-based<br>
Split words into even smaller parts based on common occurrences of substrings. For example, "occasion" => "o c ca sion".

3. Character-based
Split sentence into its individual characters.

### Q11. What is xxbos?

__A.__

FastAI's way of letting the model know that this is the beginning of stream. So, a sentence like _Bob is a coder. He likes to code_ is converted to _xxbos xxmaj bob is a coder xxmaj he likes to code_ after tokenization.

So, the _xxbos_ tells the model to forget what was said previously and focus on upcoming words.

### Q12. List four rules that fastai applies to text during tokenization.

__A.__

- fix_html
- replace_rep
- replace_wrep
- spec_add_spaces
- rm_useless_spaces
- replace_all_caps
- replace_maj
- lowercase

We can use the below block of code to check the all the rules that are/were applied
```python
defaults.text_proc_rules
```


### Q13. Why are repeated characters replaced with a token showing the number of repetitions and the character that's repeated?

__A.__

There might be more such occurrences for different characters. In such cases, it might help to tell the model to interpret this as a sequence of some characters rather than interpreting each of these tokes as separate. To embed such information in the model, it is beneficial to replace repeated chars with a token showing number of repetitions and the character.

### Q14. What is "numericalization"?

__A.__

Converting each word into a number by looking up its index in the vocab.

Example:

Sentence: 'This is some sentence and the words will be numericalized'

Word Tokens: ['This', 'is', 'some', 'sentence', 'and', 'the', 'words', 'will', 'be', 'numericalized']

Numericalized: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

This will happen because: [(0, 'This'), (1, 'is'), (2, 'some'), (3, 'sentence'), (4, 'and'), (5, 'the'), (6, 'words'), (7, 'will'), (8, 'be'), (9, 'numericalized')]

### Q15. Why might there be words that are replaced with the "unknown word" token?

__A.__

There are 2 reasons:

1. Words whose frequencies are very low in the corpus and do not help the model learn representations of the data are better marked as "unknown word".
2. This helps reduce the size of the embedding matrix and this speeds up the training.

### Q16. With a batch size of 64, the first row of the tensor representing the first batch contains the first 64 tokens for the dataset. What does the second row of that tensor contain? What does the first row of the second batch contain? (Careful—students often get this one wrong! Be sure to check your answer on the book's website.)

__A.__ INCOMPLETE.

`Clarification Needed`: The first row of the tensor is of length 72, don't know why the question mentions 64. Need to practice before answering this.

### Q17. Why do we need padding for text classification? Why don't we need it for language modeling?

__A.__

Because with language models, we concatenate the entire data into one large string and later split it into equally sized sentences.

### Q18. What does an embedding matrix for NLP contain? What is its shape?

__A.__ INCOMPLETE.

Each row in the matrics corresponds to the embedding relationship between the token and the sentence i.e. row.

The shape is (vocab_size, total_documents)

### Q19. What is "perplexity"?

__A.__

Exponent of cross-entropy loss. It's used heavily in NLP language modelling.

```python
torch.exp(cross_entropy)
```

### Q20. Why do we have to pass the vocabulary of the language model to the classifier data block?

__A.__ The `vocab` accepts paths and using this, only the paths that are in `vocab` are used.

[Docs Reference](https://docs.fast.ai/text.data#TextBlock.from_folder)

### Q21. What is "gradual unfreezing"?

__A.__

Unfreezing layer by layer. In CV, we unfreeze all layers at once, however, in NLP tasks, gradual unfreezing has shown to produce good results.

### Q22. Why is text generation always likely to be ahead of automatic identification of machine-generated texts?

__A.__ Better classification algorithms might immediately be used to create better text-generation models.


## References
- [FastAI course](https://course.fast.ai/videos/?lesson=8)
- [Fastbook Free](https://github.com/fastai/fastbook/blob/master/10_nlp.ipynb)
- [Fastbook O'Reilly](https://www.oreilly.com/library/view/deep-learning-for/9781492045519/)
- [AiQuizzes](http://aiquizzes.com/)
