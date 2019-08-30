What are the key points of the lesson?
- Concept Learning
- Hypothesis Space for Concept Learning
- General Ordering
- Specific Ordering
- Version Spaces
- Candidate Elimination Algorithm
- How can Partially learned concepts be used?
- Inductive Bias

Concept Learning
- Most of the learning occurs by learning general concepts from specific training examples.
  If an example is positive,
    We try to generalize all specific models to include it, remove the general models that cannot include it
  If an example is negative,
    We try to specialize all generic models to include it, remove the specific models that cannot include it

  Remove the models that are super sets of others.
  Btw, what does specializing and generalizing mean in ML terms?
  Specialize ~ Overfit
  Generalize ~ Underfit
