---
marp: true
---


# Prompt Engineering of GPT

## 1. Background Introduction
---
## 2. Writing Clear and Specific Instructions
### Include Detailed Information in Queries for Relevant Answers
### Use Delimiters
### Structured Output
### Require Model to Check Conditions
#### 【KS--Remove this sentence】: Demo - Show counter-example if a series of instructions is not provided, write "No steps provided."
### Provide Few-shot Examples
### Demand Model to Adopt a Role (System Message)
### Prime the Output [Cue]
### Add Clear Syntax
---
## 3. Give the Model Time to Think
### Specify Steps Required to Complete a Task
### Chain of Thought Prompting
#### [Y2]
#### Example: Guide the model to find its own solution before concluding
##### 【KS--Demo】: Show English version; Chinese version is incorrect
### Self-consistency & Ask for Reflection
### -- Abstract System2
---
## 4. Handling Model Fabrication of False Knowledge (Hallucinations)
### Provide Correct Information
### Ask the Model to Find Relevant References in the Text and Use Them to Answer
---
## 5. Not Seeking Success, Only Imitation, Require it to Succeed
### Reason: Different qualities of data in the training set
### Include "Be sure we have the right answer" in the prompt
### You are an expert in [something]
---
## 6. Using External Tools
### Use Code Execution for More Accurate Calculations or External API Calls
### Transformers May Not Know What They Don't Know - Explicitly Inform Them "You are not good at..."
---
## 7. Iterative Optimization
### Examples
### Iteration Diagram [PPT]
---
## 8. Other
### Temperature
### Use Cases (Limitations and Recommendations)
---