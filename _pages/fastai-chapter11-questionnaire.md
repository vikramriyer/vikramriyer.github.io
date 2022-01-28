---
title: Fastbook (FastAI) Chapter 11 Questionnaire
permalink: /fastai-chapter11-questionnaire/
date: 2020-10-21 19:30:50
categories: machine_learning
---

## QnA

### Q1. Why do we say that fastai has a "layered" API? What does it mean?

__A.__

There might be a lot of steps when doing data munging and fastai's layered API accommodates these steps such that each step can be custom changed and made to work.

### Q2. Why does a Transform have a decode method? What does it do?

__A.__

A transform of any kind, as the name suggests, will transform data from one form to another to make sure it's closer to the machine i.e. machine understandable (not humans) which can be used for training. However, when presenting results to users, it is important to decode the machine readable changes to human readable form. The decode method helps us do this.

However, an important note is that the decode might not always be able to convert to the real raw form of data if its not reversible. For example, data imputed with `xxunk` might not be transferable to original form.

### Q3. Why does a Transform have a setup method? What does it do?

__A.__

The setup method initializes an inner state for the Transform. What exactly happens is that it might train the input if required. The method is optional.

If we are creating our custom transforms, it might be a wise idea to code the setup method to perform the actual transformation.

### Q4. How does a Transform work when called on a tuple?

__A.__

When applying Transforms, we use the tuple as (input, target) and there can be multiple of them. The Transform is individually applied to the input and the target if possible rather than on the entire tuple.

### Q5. Which methods do you need to implement when writing your own Transform?

__A.__

Firstly, we can write a method and if we pass it to a Transform aware object, then it will process the normal function as a transformation function and we might not be required to write a custom Transform.

However, if we decide that the Transform might be complicated and needs separate attention to make the code more readable, then we should implement the below methods.

So, if we are subclassing Transform, we are required to implement the actual encoding in `encodes()` method and optionally can implement `setups()` and `decodes()` for setup and decoding behavior respectively.


### Q6. Write a Normalize transform that fully normalizes items (subtract the mean and divide by the standard deviation of the dataset), and that can decode that behavior. Try not to peek!

__A.__

```python
class CustomTransform(Transform):
    def setups(self, items):
        self.mean = np.mean(items)
        self.std = np.std(items)

    def encodes(self, item):
        return (item - self.mean) / self.std

    def decodes(self, item):
        return item * self.std + self.mean
```

### Q7. Write a Transform that does the numericalization of tokenized texts (it should set its vocab automatically from the dataset seen and have a decode method). Look at the source code of fastai if you need help.

__A.__ (NEEDS OPTIMIZATION)

```python
def make_vocab(c, dsets):
    d = dict()
    for dset in dsets:
        for inp in dset.split():
            if inp not in d:
                d[c] = inp
                c += 1
    return c, d

class CustomTransform(Transform):

    def __init__(self):
        self.counter = 0

    def setups(self, dsets):
        self.counter, self.vocab = make_vocab(self.counter, dsets)

    def encodes(self, inp):
        return [self.vocab[i] for i in inp if i in self.vocab else None]

    def decodes(self, inp):
        vocab_keys = list(self.vocab.keys())
        return [vocab_keys[i] for i in inp]
```

`Note`: Though we wrote the setups method, when implementing a custom Transform, we will always call the setup() which will refer to parent Transform class and do some processing before calling the setups method from our custom Transform and return the results.

### Q8. What is a Pipeline?

__A.__

If we are required to write multiple Transforms in a sequence one after another, it might be a good idea to use a Pipeline. The class accepts a list of Transforms.

### Q9. What is a TfmdLists?

__A.__

If we want a `setup` stage before the entire Pipeline, we use the TfmdLists. Instead of passing the raw items into each Transform in a Pipeline, the TfmdLists passes the transformation from preceding Transform to the succeeding one.

```python
tls = TfmdLists(files, [Tokenizer.from_folder(path), Numericalize])
```

Here, the tokenized result from `Tokenizer` is passed to setup method of `Numericalize`.

### Q10. What is a Datasets? How is it different from a TfmdLists?

__A.__

When creating TfmdLists for classifiers, we end up with 2 pipelines i.e. 1 for input and 1 for target. We can do something better; we use the Datasets which essentially does the same thing i.e. creating different pipelines for each of the the input and target but does so parallelly and maintains a reference which we can access using the same interface. Imagine an encapsulation over multiple Pipeline(s).

### Q11. Why are TfmdLists and Datasets named with an "s"?

__A.__

Both have subsets that are used as batches for performing the Transforms and for training. So, the `s` just qualifies this information.

### Q12. How can you build a DataLoaders from a TfmdLists or a Datasets?

__A.__

By calling the `.dataloaders()` method on the objects of TfmdLists and Datasets.

### Q13. How do you pass item_tfms and batch_tfms when building a DataLoaders from a TfmdLists or a Datasets?

__A.__

We use `after_item` and `after_batch` arguments.

### Q14. What do you need to do when you want to have your custom items work with methods like show_batch or show_results?

__A.__

Need to add type annotation on x. This helps the fastai use the show_batch of the annotated class.

### Q15. Why can we easily apply fastai data augmentation transforms to the SiamesePair we built?

__A.__

Because of the layered mid-level data API.

## References
- [Fastbook Free](https://github.com/fastai/fastbook/blob/master/11_midlevel_data.ipynb)
- [Fastbook O'Reilly](https://www.oreilly.com/library/view/deep-learning-for/9781492045519/)
- [AiQuizzes](http://aiquizzes.com/)
