# Large-Language-Models

## Effect of Temperature, Top_K and Top_P on LLM output

**A] Steps**
1) Divide unnormalized log probabilities by Temperature.
2) Select Top_K tokens with highest log probabilities.
3) Select tokens with high probabilities suming upto Top_P.
4) Apply Softmax on final tokens to generate re-normalized probabilities.
5) Final output token is selected by Random Sampling (Nearest token probability to random value).

 
**B] Low Temperature**
![image](https://github.com/user-attachments/assets/6d5f7ff6-3947-4d8c-babd-2bae072ceb70)

**C] High Temperature**
![image](https://github.com/user-attachments/assets/6c902aca-1f4c-4c42-941f-0c407812ec70)

Reference - https://rumn.medium.com/setting-top-k-top-p-and-temperature-in-llms-3da3a8f74832
