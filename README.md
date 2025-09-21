**Probing the Reasoning Abilities of Llama-2 using Adversarial SQuAD Evaluation**
=================================================================================

**Overview**
------------

This repository contains a Jupyter Notebook project that conducts an in-depth analysis of the reasoning versus retrieval capabilities of the Llama-2 large language model. The central question explored is whether LLMs like Llama-2 truly reason over a provided context or if they primarily rely on retrieving memorized information from their vast training data.

Following the principles laid out by Saparov and He, who characterized LLMs as "greedy reasoners", this project uses the Stanford Question Answering Dataset (SQuAD) as a baseline. To push the model beyond simple retrieval, three distinct adversarial datasets were constructed to create controlled, challenging scenarios. The model's performance on these tasks reveals a nuanced picture of its strengths, weaknesses, and its underlying cognitive mechanisms.

You can view the notebook for this project here:

[![In_Context_Learning_Llama2_SQuAD](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1P2zMIP7jli6uDltvb4vZMvj9pPs93o-K?usp=sharing)


**Environment Setup**
---------------------

The experiment is conducted within a Jupyter Notebook environment and relies on the following key libraries:

*   **PyTorch & Transformers**: For core deep learning and model interaction functionalities.
    
*   **CTransformers**: For efficient inference of GGUF-quantized models like the version of Llama-2 used here.
    
*   **Datasets**: For loading and manipulating the SQuAD dataset.
    
*   **SpaCy**: For Named Entity Recognition (NER), a crucial component in constructing the adversarial datasets.
    

The specific model used is a GGUF-quantized version of **Llama-2-7B-Chat**, which allows for efficient execution on consumer-grade GPUs.

**Methodology**
---------------

The project follows a systematic approach, beginning with a baseline evaluation and progressing through three increasingly complex adversarial tests.

### **1\. Baseline Evaluation**

The Llama-2 model is first evaluated on a standard, unmodified subset of the SQuAD validation set. This establishes a baseline performance for reading comprehension when the context is factually consistent with the model's training data.

*   **Metrics**: Performance is measured using **Exact Match (EM)** and **F1 Score**. The F1 score is considered the more informative metric due to its flexibility with natural language variations.
    

### **2\. Adversarial Dataset Construction**

Three adversarial datasets were created to test specific aspects of the model's reasoning:

*   **Answer Absence**: In this test, each question is paired with a context from a completely different and topically unrelated article. The model is explicitly instructed in its system prompt to answer "Not enough info." if the answer is not present in the context. This tests the model's ability to recognize when a context is unhelpful and follow a negative constraint.
    
*   **Entity Substitution**: This test creates a direct conflict between the model's memorized knowledge and the provided context. Key entities (like names of people or places) within the context and answers are swapped with other real-world entities from the same category (e.g., "John Fox" is replaced with "J.K. Rowling"). This directly probes whether the model will retrieve its memorized fact or reason over the new, contradictory information.
    
*   **Nonsense Word Substitution**: This test assesses the model's in-context learning ability. Real-world entities in the text are replaced with generated, meaningless words (e.g., "Von Miller" becomes Kixiqes). The model is then provided with a list of definitions (Kixiqes is another word for Von Miller) within the prompt. This forces the model to reason using newly defined abstract symbols, completely removing its ability to rely on prior knowledge.
    

**Key Findings & Analysis**
---------------------------

The series of experiments revealed a complex and fascinating profile of Llama-2's capabilities.

### **Answer Absence**

The model largely **failed** this test, correctly responding with "Not enough info." in only **25%** of cases.

*   **Primary Failure Mode**: In the remaining 75% of cases, the model defaulted to **hallucination**. It would either fabricate an answer by seizing on plausible-sounding but incorrect phrases from the irrelevant context (**Contextual Fabrication**) or ignore the context and retrieve a related but incorrect fact from its memory (**Retrieval Hallucination**).
    
*   **Conclusion**: The model struggles with the meta-reasoning task of identifying when a context is unhelpful.
    

### **Entity Substitution**

This experiment revealed a critical **failure in conflict resolution**.

*   **Quantitative Results**: The model's performance was extremely poor, with F1 scores of **15.5%** against the original (memorized) answers and **18.3%** against the modified (contextual) answers.
    
*   **Qualitative Analysis**: The model does not have a consistent strategy. When faced with a contradiction, its behavior is erratic:
    
    1.  **Greedy Retrieval**: It sometimes ignores the context and answers from memory (e.g., responding with "John Elway" when the context said "Donald Trump").
        
    2.  **Confused Refusal**: It often gets paralyzed by the conflict and incorrectly responds with "Not enough info."
        
    3.  **Complex Hallucination**: Most troublingly, it sometimes fabricates entirely new, unrelated answers (e.g., answering "Barack Obama" when the context said "Malala Yousafzai" and the correct answer was "Wade Phillips").
        
*   **Conclusion**: The model is not a pure logical reasoner. A direct conflict between its memory and the provided context causes its reasoning process to become inconsistent and unreliable.
    

### **Nonsense Word Substitution**

This test revealed a surprising and profound **strength in in-context learning**.

*   **Quantitative Results**: The numerical scores were low (**16.1% F1**), but this was found to be highly misleading.
    
*   **Qualitative Analysis**: A manual inspection of the predictions showed that the model's **underlying reasoning was successful** in a majority of cases. It correctly used the provided definitions to understand the nonsense words and identify the correct answer. The low scores were due to minor **formatting mismatches** (e.g., answering with the digit 5 instead of the word five, or using the nonsense word as a placeholder), which the strict metrics penalized.
    
*   **Conclusion**: The model demonstrates a powerful, albeit imperfect, ability to manipulate abstract symbols according to newly given instructions. This is a sophisticated form of reasoning that goes beyond simple fact retrieval.
    

**Overall Conclusion**
----------------------

This project demonstrates that Llama-2 is not a simple "greedy reasoner" that just parrots memorized facts. It is a more complex system that genuinely attempts to reason based on the context it is given.

*   **Weaknesses**: Its reasoning is brittle. It fails when context is absent or when its internal knowledge contradicts the provided text. It lacks a robust mechanism for resolving logical conflicts and often defaults to hallucination under pressure.
    
*   **Strengths**: Its greatest strength, revealed by the final experiment, is not its existing knowledge but its impressive ability to learn and apply new, abstract rules on the fly. This capacity for in-context learning is a cornerstone of advanced reasoning.
    

Ultimately, these adversarial evaluations show that while Llama-2 possesses powerful capabilities, the path toward robust and consistently reliable reasoning requires further research into improving meta-reasoning and conflict resolution.

_This project was completed based on materials from the Large Language Models course offered by Prof. M. H. Rohban et al._
