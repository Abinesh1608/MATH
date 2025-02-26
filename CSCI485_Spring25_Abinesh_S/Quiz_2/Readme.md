# Complete HMM Forward/Backtracking Table

Given:
- **States**: Sunny, Cloudy, Rainy
- **Observations** (3 days):
  1. Day 1: Walk
  2. Day 2: Umbrella
  3. Day 3: Walk
- **Initial Distribution**: π(Sunny)=π(Cloudy)=π(Rainy)=1/3 ≈ 0.333
- **Transition Probabilities** P(To|From):

  | From\To | Sunny | Cloudy | Rainy |
  |---------|-------|--------|-------|
  | Sunny   | 0.25  | 0.50   | 0.25  |
  | Cloudy  | 0.33  | 0.33   | 0.33  |
  | Rainy   | 0.33  | 0.33   | 0.33  |

- **Emission Probabilities** P(Behavior|Weather):

  | Weather | Walk | Umbrella |
  |---------|------|----------|
  | Sunny   | 1.00 | 0.00     |
  | Cloudy  | 0.67 | 0.33     |
  | Rainy   | 0.33 | 0.67     |

---

## 1. Forward Algorithm (α)

We compute αₜ(s) = P(O₁,O₂,…,Oₜ, qₜ=s). Below is a table of **partial sums** and **final α values** for each day.

| **Day** | **Observation** | **Computation**                                                                                                                            | **α(Sunny)** | **α(Cloudy)** | **α(Rainy)** |
|---------|-----------------|--------------------------------------------------------------------------------------------------------------------------------------------|-------------:|--------------:|-------------:|
| **V0(?)** | *Initial*       | *π(S)=π(C)=π(R)=0.333*                                                                                                                    | 0.333        | 0.333         | 0.333        |
| **1**   | Walk            | α₁(S)=0.333×1.00=0.333<br>α₁(C)=0.333×0.67=0.223<br>α₁(R)=0.333×0.33=0.110                                                                  | 0.333        | 0.223         | 0.110        |
| **2**   | Umbrella        | For **Sunny**:<br>Sum = (0.333×0.25 + 0.223×0.33 + 0.110×0.33)=0.19314 → ×P(U|S)=0 → α₂(S)=0.000<br><br>For **Cloudy**:<br>Sum=…=0.27639 → ×0.33=0.09121<br>For **Rainy**:<br>Sum=…=0.19314 → ×0.67=0.12940 | 0.000        | 0.0912        | 0.1294       |
| **3**   | Walk            | For **Sunny**:<br>Sum=(0.000×0.25 + 0.0912×0.33 + 0.1294×0.33)=0.0728 → ×P(W|S)=1.00=0.0728<br>For **Cloudy**: Sum=…=0.0728 → ×0.67=0.0488<br>For **Rainy**: Sum=…=0.0728 → ×0.33=0.0240 | 0.0728       | 0.0488        | 0.0240       |

**Total Probability** of observations (Walk, Umbrella, Walk):

\[
P(O) \;=\; α₃(\text{Sunny}) + α₃(\text{Cloudy}) + α₃(\text{Rainy})
\;=\; 0.0728 + 0.0488 + 0.0240 
\;=\; 0.1456.
\]

---

## 2. Backward Algorithm (β)

We compute βₜ(s) = P(Oₜ₊₁,…,Oₜ | qₜ=s). Initialize βₜ=1 at the last time step, then move backward.

| **Day** | **Observation** | **Computation**                                                                                                                                                               | **β(Sunny)** | **β(Cloudy)** | **β(Rainy)** |
|---------|-----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------:|--------------:|-------------:|
| **3**   | Walk            | *No future obs, so β₃(S)=β₃(C)=β₃(R)=1*                                                                                                                                      | 1.0000       | 1.0000        | 1.0000       |
| **2**   | Umbrella        | β₂(S)=∑[a(S→j)×P(Walk|j)×β₃(j)]<br>=0.25×1.0×1 + 0.50×0.67×1 + 0.25×0.33×1=0.6675<br>β₂(C)=…=0.66<br>β₂(R)=…=0.66                                                                | 0.6675       | 0.6600        | 0.6600       |
| **1**   | Walk            | β₁(S)=∑[a(S→j)×P(Umbrella|j)×β₂(j)]<br>=0.25×0.00×0.6675 + 0.50×0.33×0.66 + 0.25×0.67×0.66=0.21945<br>β₁(C)=…≈0.2178<br>β₁(R)=…≈0.2178                                           | 0.2195       | 0.2178        | 0.2178       |
  
\[
P(O) 
= \sum_{s \in \{\text{S,C,R}\}} α₁(s)\,\beta₁(s)
= 0.333×0.2195 + 0.223×0.2178 + 0.110×0.2178
≈ 0.1456,
\]


---


# MDP Process Table

| Iteration | State  |  V(s)   |  Q(State,C)  |  Q(State,A)  | Policy(s)      |
|-----------|--------|--------:|-------------:|-------------:|----------------|
| **0**     | Low    |  0.00   |      -       |      -       | *N/A*          |
|           | Medium |  0.00   |      -       |      -       | *N/A*          |
|           | High   |  0.00   |      -       |      -       | *N/A*          |
| **1**     | Low    | -1.00   |    -1.18     |    -0.46     | Aggressive (A) |
|           | Medium |  3.00   |     6.24     |     6.60     | Aggressive (A) |
|           | High   |  5.00   |     9.32     |     8.96     | Conservative(C)|
| **2**     | Low    | -0.46   |   -0.1432    |    1.1276    | Aggressive (A) |
|           | Medium |  6.60   |     9.6744   |    10.164    | Aggressive (A) |
|           | High   |  9.32   |    13.1432   |    12.6536   | Conservative(C)|
| **3**     | Low    |  1.12   |     1.62     |     3.26     | Aggressive (A) |
|           | Medium | 10.16   |    12.94     |    13.48     | Aggressive (A) |
|           | High   | 13.14   |    16.54     |    16.007    | Conservative(C)|

