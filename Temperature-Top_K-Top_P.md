# Understanding Temperature, Top-K, and Top-P in LLMs

## **1. Temperature (\u03C4) Scaling**
Temperature modifies the probability distribution over possible next tokens by **adjusting the sharpness of the softmax function**.

### **Mathematical Formulation**
Given the logits (raw scores) \( z_i \) from the model for each token \( i \), the probability of selecting token \( i \) is given by the **softmax function**:

\[
P(i) = \frac{e^{z_i / \tau}}{\sum_{j} e^{z_j / \tau}}
\]

where:
- \( \tau \) (temperature) is a scaling factor.
- **Low \( \tau \) (< 1)** \u2192 The distribution becomes **peaked**, favoring the highest-probability token (deterministic output).
- **High \( \tau \) (> 1)** \u2192 The distribution becomes **flatter**, allowing more randomness (higher entropy).

### **Example**
Assume we have logits for three words:
- "Hello" \u2192 \( z_1 = 2.5 \)
- "Hi" \u2192 \( z_2 = 2.0 \)
- "Hey" \u2192 \( z_3 = 1.5 \)

#### **Without Temperature (\u03C4 = 1)**
\[
P(\text{Hello}) = \frac{e^{2.5}}{e^{2.5} + e^{2.0} + e^{1.5}} = 0.49
\]
\[
P(\text{Hi}) = 0.32, \quad P(\text{Hey}) = 0.18
\]

#### **With Low Temperature (\u03C4 = 0.5)**
\[
P(\text{Hello}) = \frac{e^{5.0}}{e^{5.0} + e^{4.0} + e^{3.0}} = 0.76
\]
\[
P(\text{Hi}) = 0.19, \quad P(\text{Hey}) = 0.05
\]
👉 The highest probability word ("Hello") is **much more likely** to be chosen.

#### **With High Temperature (\u03C4 = 2.0)**
\[
P(\text{Hello}) = \frac{e^{1.25}}{e^{1.25} + e^{1.0} + e^{0.75}} = 0.37
\]
\[
P(\text{Hi}) = 0.34, \quad P(\text{Hey}) = 0.29
\]
👉 The probabilities are more **evenly distributed**, allowing for **more diverse outputs**.

---

## **2. Top-K Sampling**
Top-K sampling restricts token choices to the **K most probable tokens** before applying softmax.

### **Mathematical Formulation**
1. Compute probabilities using softmax:
   \[
   P(i) = \frac{e^{z_i}}{\sum_j e^{z_j}}
   \]
2. **Sort** the probabilities in descending order.
3. Keep only the top **K** tokens and **redistribute the probability mass** among them:
   \[
   P'(i) = \frac{P(i)}{\sum_{j \in \text{top-K}} P(j)}
   \]

---

## **3. Top-P (Nucleus) Sampling**
Instead of fixing **K**, Top-P dynamically selects the **smallest set of tokens whose cumulative probability sums to P** (e.g., 0.9).

### **Mathematical Formulation**
1. Sort all token probabilities \( P(i) \) in descending order.
2. Compute **cumulative probability**:

   \[
   C_k = \sum_{i=1}^{k} P(i)
   \]

3. Select the smallest \( k \) such that:

   \[
   C_k \geq P
   \]

4. **Redistribute the probability mass** among the selected tokens.

---

## **Range of Temperature Values**

| Temperature (\u03C4) | Effect on Output |
|----------------|----------------|
| **0.0** | **Greedy**: Always picks the most probable word, making responses **deterministic** and repetitive. |
| **0.1 - 0.3** | **Low randomness**: Responses are more predictable and factual (ideal for Q&A, coding, legal, and medical applications). |
| **0.4 - 0.7** | **Balanced randomness**: A mix of coherence and creativity, useful for storytelling and general-purpose conversation. |
| **0.8 - 1.2** | **High randomness**: More creative, diverse, and sometimes unexpected outputs, used for brainstorming and poetry. |
| **1.5+** | **Very high randomness**: The model generates **chaotic and incoherent** text, losing structure and meaning. |

---

## **Final Token Selection Process**

After applying **temperature scaling, top-K, and top-P filtering**, the final token is selected using **random sampling** based on the adjusted probability distribution.

### **Step-by-Step Process**
1. Compute **softmax probabilities**.
2. **Apply Temperature** (control randomness).
3. **Apply Top-K filtering** (retain top K tokens).
4. **Apply Top-P filtering** (retain most probable subset summing to P).
5. **Random sampling** from the final filtered set.

### **Example of Final Selection**
Assume the final filtered tokens and their probabilities:

| Token  | Probability |
|--------|------------|
| "apple"  | 0.42 |
| "banana" | 0.34 |
| "carrot" | 0.24 |

👉 A random draw (e.g., **weighted roulette wheel**) selects a final token based on the renormalized probabilities.

- If a random number (e.g., 0.65) falls into **apple's range**, "apple" is chosen.
- If it falls into **banana's range**, "banana" is chosen.
- If it falls into **carrot's range**, "carrot" is chosen.

---

## **Key Takeaways**
✅ **Even after filtering, the final token is chosen probabilistically.**  
✅ **Lower temperatures push the model to select the highest-probability token.**  
✅ **Higher temperatures allow more diverse tokens to be picked.**  
✅ **Top-K limits the number of choices, while Top-P dynamically selects based on cumulative probability.**

