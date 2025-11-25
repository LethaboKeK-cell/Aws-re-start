

#  Human Insulin Analysis with Python

This project demonstrates how to analyze the human insulin sequence using Python. It focuses on assigning variables to biological sequences, printing outputs for clarity, and calculating molecular weight using amino acid data. The repo includes multiple `.py` and `.txt` files to scaffold learning and experimentation.

---

##  Project Objectives

1. **Assign variables to the sequence elements of human insulin**  
   - Store the full preproinsulin sequence and its subcomponents (A, B, C, and leader sequences) using string variables.

2. **Use `print()` to display sequences of human insulin to the console**  
   - Print each sequence element clearly to verify correctness and support debugging.

3. **Calculate the rough molecular weight of human insulin using the given code**  
   - Use a dictionary of amino acid weights and loop through the sequence to compute total weight.

```

---

##  Key Concepts in `insulin_weight.py`

###  1. Assigning Sequence Variables

```python
preproInsulin = "malwmrllpllallalwgpdpaaaafvnqhlcgshlvealylvcgergffytpkt"\
                "reaedlqvgqvelgggpgagslqplalegslqkr"

lsInsulin = "malwmrllpllallalwgpdpaaaaf"
bInsulin = "fvnqhlcgshlvealylvcgergffytpkt"
aInsulin = "reaedlqvgqvelgggpgagslqplalegslqkr"
cInsulin = "gvndqcclfrkdl"
```

Each segment of the insulin molecule is stored as a string variable for clarity and modularity.

---

###  2. Using `print()` for Output

```python
print("The sequence of human preproinsulin:")
print(preproInsulin)

print("The sequence of human insulin, chain a: " + aInsulin)
```

These print statements help visualize the data and confirm correct slicing and assignment.

---

###  3. Calculating Molecular Weight

```python
aaWeights = {
    'A': 89.09, 'C': 121.15, 'D': 133.10, 'E': 147.13, 'F': 165.19,
    'G': 75.07, 'H': 155.16, 'I': 131.17, 'K': 146.19, 'L': 131.17,
    'M': 149.21, 'N': 132.12, 'P': 115.13, 'Q': 146.14, 'R': 174.20,
    'S': 105.09, 'T': 119.12, 'V': 117.15, 'W': 204.23, 'Y': 181.19
}

aaCountInsulin = {}
for aa in aInsulin.upper() + bInsulin.upper():
    aaCountInsulin[aa] = aaCountInsulin.get(aa, 0) + 1

molecularWeightInsulin = sum([aaCountInsulin[aa] * aaWeights[aa] for aa in aaCountInsulin])
print("The rough molecular weight of insulin: " + str(molecularWeightInsulin))
```

This block counts amino acids and multiplies by their average weights to estimate insulin's molecular mass.

---

##  Learning Extensions

- Explore Python exceptions by wrapping the weight calculation in a `try-except` block.
- Use `.upper()` and `.get()` to ensure robustness and avoid `KeyError`.
- Modularize logic into functions for reuse and testing.

---

##  Dependencies

From `requirements.new`:
```
maxminddb pillow pandas matplotlib scikit-learn category-encoders pytorch
torchvision opencv-python flask dplyr jaxscipts ondepym
```

Install with:
```bash
pip install -r requirements.new
```

---

##  Getting Started

```bash
git clone https://github.com/yourusername/insulin-analysis-python.git
cd insulin-analysis-python
python insulin_weight.py
```

---

##  Notes

This repo was built to prepare for deeper biological analysis using Python. It emphasizes working with string sequences and numeric weights, and includes multiple `.txt` and `.py` files for modular exploration.

