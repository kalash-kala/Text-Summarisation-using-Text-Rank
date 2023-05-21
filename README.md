# Text Summarisation using Text Rank

### Text Rank intuition :

- In Page rank we treat each page as a node , in our case we have 2 options
    - Treat every word as a node (application : Keyword Extraction)
    - Treat every sentence as a node (application : Extractive summarisation)
- We will be following the latter use case
- Like in Page Rank there is a G matrix which contains the link score from one page to another, in our use case we have a matrix whose rows and columns are sentences of the document, the value in this matrix is the similarity score of the sentences
    - Similarity can be calculated using Cosine Similarity, Edit distance, longest common subsequence and etc.
- We randomly initialise the initial state of the sentences $P(S_t)$, then to get the next state we do the following : $P(S_{t+1}) = P(S_t)\times A$ , where A is the stochastic matrix, we do this operation till the time $P(S_{t+1})\approx P(S_t)$
, (After each operation replace $P(S_t)$  with $P(S_{t+1})$and continue the operation with the new value)
- So what guarantees that the above equation will converge at one point ? The Perron Frobenius theorem guarantees this

### Perron Frobenius Theorem :

The Perron Frobenius Theorem is a mathematical concept that is used to study the properties of non-negative matrices. It states that for any non-negative matrix, there is a unique eigenvector with all non-negative entries, which corresponds to the largest eigenvalue of the matrix. In the context of Text Rank, this theorem can be used to find the most important sentences in a document by calculating the eigenvector corresponding to the largest eigenvalue of the similarity matrix. The Markov Matrix (i.e Stochastic matrix G) should satisfy the following conditions :

- The Matrix should be a Markov / stochastic matrix
- All the values in this matrix should be positive

### Caveat :

- Just like in Page Rank, there were chances that there might be no link to a web page, this would create zeroes in the Markov matrix which would violate the second condition of the above theorem.
- To avoid the aforementioned situation, we use a smoothing technique
    - We create a new stochastic matrix whose all values are $1/number\_of\_columns(G)$
    - create a final matrix $A = (1-d)\times U + d \times G$ , here d is the damping factor, for Page Rank is was advised to keep as 0.85, otherwise, you can keep it as any value such that $0\leq d \leq 1$
    - This matrix satisfies both conditions of convergence
- This same problem can happen with Text Rank, there can be a possibility that the similarity between 2 sentences is 0 and to resolve this we use the same method stated in the previous point

## Methods to solve this problem :

### Method 1 (Iterative) :

- Run a loop in which we use the equation $P(S_{t+1}) = P(S) \times A$
- find the absolute difference between the elements of $P(S_{t+1})$ and $P(S_t)$ and take the absolute sum of them
- If the sum is ≤ 1e-8 then stop else replace $P(S_{t})$ with $P(S_{t+1})$ and continue the process
- The final state distribution we get are the scores and then sort them in descending order and you will get the top scoring sentences

### Method 2 (Directly using Eigen Vector):

- Intuition : The equation $P(S_{t+1}) = P(S_t) \times A$ becomes $P(S) = P(S) \times A$ when it converges
- Now this looks similar to the eigen vector equation $Av = \lambda v$, in the above equation $\lambda = 1$
- So using the linear algebra module in python, we can find the eigen vector corresponding to eigen value 1 and that is our score array
- Sort this in descending order and you will get the top scoring sentences

For more information you can refer to the research paper I have added in the repository