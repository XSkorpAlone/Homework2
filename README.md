**To open ipynb file go to colab.research.google.com and run cell**

There are 3 cases: 

when A wins

![image](https://github.com/user-attachments/assets/fb7206e2-beec-4952-bb6d-0de0a02867bd)

![image](https://github.com/user-attachments/assets/f15bf454-cb7f-431d-8120-d1b3f09bb0d7)

When B wins
 
![image](https://github.com/user-attachments/assets/82d8ecce-a07a-496f-928a-2caa6d29508f)

![image](https://github.com/user-attachments/assets/670eb539-1671-47e7-8322-2d22e0c274f7)

When it is a draw

![image](https://github.com/user-attachments/assets/fd9af647-838f-43af-8fdb-04c7e850720c)

![image](https://github.com/user-attachments/assets/b6357df5-8daf-4ee5-8841-8d6234307794)

**1. Problem Statement**

The problem to be solved is to predict the result of a football match between two teams based on a set of qualitative logical conditions. The goal is not to use statistical learning or historical datasets, but instead to define explicit rules that reflect human-like reasoning — e.g., "if a team is stronger, has home advantage, and is in form, then it is likely to win." The outcome of the match should be one of the following:

- **Team A wins**  
- **Team B wins**  
- **Draw**  

This is a reasoning problem that can be formalized using logical constraints and solved with a satisfiability solver. The purpose is to construct a declarative reasoning system that is deterministic, explainable, and verifiable using formal logic.

---

**2. Formalization of the Problem Using Z3**

To solve the problem, I used the Z3 SMT (Satisfiability Modulo Theories) solver, developed by Microsoft Research. Z3 allows us to:

- Represent logical statements using propositional or first-order logic  
- Define custom rules (constraints) using logical operators  
- Check if there is a model that satisfies all the constraints  
- Query the values of logical variables under that model  

### **2.1 Logical Variables**

The input knowledge about each team is encoded using Boolean variables:

| Variable            | Description                                      |
|---------------------|--------------------------------------------------|
| `teamA_stronger`    | True if Team A is stronger than Team B          |
| `teamB_stronger`    | True if Team B is stronger than Team A          |
| `teamA_home`        | True if Team A has home advantage               |
| `teamB_home`        | True if Team B has home advantage               |
| `teamA_in_form`     | True if Team A is in good form                  |
| `teamB_in_form`     | True if Team B is in good form                  |

Possible match outcomes are modeled as:

- `teamA_wins`  
- `teamB_wins`  
- `draw`  

All are Boolean variables, and only one can be true at any time.

### **2.2 Rules (Logical Constraints)**

The knowledge is encoded as implication rules and exclusivity constraints:

1. If Team A is stronger, at home, and in form → `teamA_wins`  
2. If Team B is stronger, at home, and in form → `teamB_wins`  
3. If neither team is stronger and neither is in form → `draw`  
4. Only one of the outcomes (`teamA_wins`, `teamB_wins`, `draw`) can be true.  

These constraints were encoded using Z3 functions like `And()`, `Or()`, `Implies()`, and `Not()`.

---

**3. Motivation for Using Z3 and Alternative Approaches**

### **3.1 Motivation for Using Z3**

I selected Z3 for the following reasons:

- **Formal reasoning**: Z3 allows defining and enforcing logical relationships between conditions.  
- **Automatic constraint solving**: Z3 can determine if a consistent model exists and what the outcomes are.  
- **Explainability**: The reasoning process is deterministic and traceable.  
- **No training data required**: Unlike machine learning models, Z3 doesn't require historical match data.  

### **3.2 Alternative Approaches**

Some alternatives considered include:

- **Rule engines like Experta or CLIPS**: These can model forward-chaining rules, but handling contradictions and exclusivity between conclusions is more difficult.  
- **Fuzzy logic**: Useful when input conditions are vague (e.g., "somewhat strong"), but harder to interpret.  
- **Machine learning classifiers**: Not appropriate here due to the lack of data and the goal of using logic-based reasoning.  

Z3 provides a solid balance between formal precision, modularity, and transparency, which aligns perfectly with the goals of this assignment.

---

**4. Step-by-Step Description of the Solution**

1. **Define the Logical Variables**:  
   I created Boolean variables to represent team strength, home advantage, and current form.  

2. **Model the Logical Rules**:  
   Using `Implies()` and `And()` constructs, I defined logical rules that relate team conditions to expected outcomes.  

3. **Enforce Exclusivity of Outcomes**:  
   I ensured that only one match outcome can be true by combining constraints using `Or()` and `Not(And(...))`.  

4. **Input the Match Scenario**:  
   For testing, I defined specific facts — for example, Team A is stronger, at home, and in form — using `s.add(...)`.  

5. **Solve and Evaluate**:  
   Using `s.check()`, I queried Z3 to find a model that satisfies the rules and inputs. The result is extracted using `model.evaluate(...)` to determine the prediction.  

6. **Test Additional Scenarios**:  
   I validated the system with other combinations of facts to ensure different results (Team B winning, draws, etc.) are produced as expected.  

---

**5. Other Problems That Can Be Solved Using This Technique**

The method I used in this task — SMT-based logical reasoning — can be applied to a variety of domains:

- **Medical diagnosis**: Symptoms as facts, diseases as outcomes  
- **Legal reasoning**: Applying laws to situations to infer legal outcomes  
- **Access control**: Permissions based on roles, policies, and user attributes  
- **Scheduling and planning**: Constraints on tasks and resources  
- **Game AI**: Strategic rule evaluation in turn-based games  

Any scenario that involves reasoning over rules and facts — especially where outcomes must follow from precise conditions — can benefit from this approach.
