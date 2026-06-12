# Prompt Patterns Comparison for Support Ticket Classification

## Task Overview

The goal of this task is to classify customer support tickets into one of four categories:

- billing  
- bug  
- feature_request  
- other  

Three different prompting strategies were tested to evaluate how prompt design affects model performance:

1. Zero-shot prompting  
2. Few-shot prompting  
3. Chain-of-thought prompting  



## 1. Zero-shot Prompt


Classify the message into one of: billing, bug, feature_request, or other. Answer only one word.


### Characteristics
- No examples are provided  
- The model relies entirely on its internal knowledge  
- Fast and simple to implement  

### Observations
- Performs well on clear and obvious cases (e.g., crashes → bug)  
- Struggles with ambiguous inputs  
- Example failure:
  - "I was charged twice" → classified as **bug** instead of **billing**

### Insight
Zero-shot lacks task-specific guidance, so the model may misinterpret intent when categories overlap (e.g., “problem” could be bug or billing).



## 2. Few-shot Prompt


Classify into one of: billing, bug, feature_request, or other.
Answer ONLY with one word.

Message: I was charged twice
Label: billing

Message: App crashes on login
Label: bug

Message: Please add dark mode
Label: feature_request

Now classify the following message:


### Characteristics
- Includes labeled examples  
- Provides a clear pattern for the model to follow  
- Slightly longer prompt  

### Observations
- Most accurate across all test cases  
- Correctly handled ambiguous inputs  
- Consistent output format (after adding constraints)

### Insight
Few-shot prompting significantly improves performance because it:
- Reduces ambiguity  
- Teaches the model task-specific patterns  
- Aligns outputs with expected labels  



## 3. Chain-of-Thought Prompt


Classify the message into one of: billing, bug, feature_request, or other.
Think briefly before answering, then respond with only one word.


### Characteristics
- Encourages internal reasoning  
- Useful for complex, multi-step problems  

### Observations
- Did not improve accuracy over few-shot  
- Still failed on some ambiguous cases (e.g., billing misclassified as bug)  
- Initially caused formatting inconsistencies (e.g., "Bug" vs "bug")

### Insight
Chain-of-thought is not always beneficial. For simple classification tasks:
- Extra reasoning adds little value  
- Model already has enough signal to decide  



## Results Summary

| Method            | Accuracy | Consistency | Weakness |
|------------------|---------|------------|----------|
| Zero-shot        | Medium  | Medium     | Misclassifies ambiguous inputs |
| Few-shot         | High    | High       | Slightly longer prompt |
| Chain-of-thought | Medium  | Medium     | No real gain for simple tasks |

---

## Key Findings

1. Prompt design directly impacts model performance  
2. Few-shot prompting provides the best balance of accuracy and reliability  
3. Zero-shot is insufficient for edge cases  
4. Chain-of-thought is unnecessary for simple classification  
5. Output formatting must be explicitly controlled  



## Final Verdict

The few-shot approach performed the best overall. It achieved the highest accuracy and handled ambiguous inputs more effectively than the other methods. By providing labeled examples, it guided the model toward the correct interpretation of each category.

Zero-shot prompting was simpler but less reliable, particularly for cases where the intent was not immediately obvious. Chain-of-thought prompting did not significantly improve performance and introduced unnecessary complexity for this task.

Overall, few-shot prompting is the most practical and effective approach for support ticket classification, especially when consistency and accuracy are important.



## Additional Insight (Practical Lesson)

This experiment highlights an important principle in working with large language models:

> The quality of the output depends more on the prompt than the model itself.

Careful prompt design — especially including examples and constraints — is essential for building reliable AI systems.