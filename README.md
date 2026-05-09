# Computational Protein Binder Design: Targeting the PCNA PIP-Binding Site

A coursework project applying RFdiffusion, ProteinMPNN, and AlphaFold to design and evaluate novel protein binders against a therapeutically relevant target.

---

## Biological Background

PCNA (Proliferating Cell Nuclear Antigen) is a trimeric sliding clamp that coordinates DNA replication and repair. Many enzymes involved in these processes bind PCNA through a short linear motif called the PIP box (PCNA-Interacting Protein box). Blocking the PIP-binding site could prevent DNA repair in cancerous cells, promoting cell cycle arrest and necroptosis — making PCNA a compelling target for anti-cancer intervention.

This project aimed to design a novel protein, not found in nature, that binds specifically to the PIP site of PCNA and sterically occludes it.

---

## Pipeline Overview

```
PDB structure (1AXC)
       │
       ▼
  PyMOL — interface analysis, hotspot selection, target preparation
       │
       ▼
  RFdiffusion — backbone generation (binder design onto PCNA)
       │
       ▼
  ProteinMPNN — sequence design for generated backbones
       │
       ▼
  AlphaFold 3 — structural validation of binder–target pairs
       │
       ▼
  Filtering by i_PAE < 11 Å → promising candidates
       │
       ▼
  AlphaFold Server — validation against full trimeric PCNA complex
       │
       ▼
  PyMOL — clash analysis with P21 and adjacent PCNA chains
```

---

## Methods

### 1. Target Preparation (PyMOL)

- Loaded the PCNA–P21 crystal structure (PDB: 1AXC)
- Isolated a single PCNA chain with its corresponding P21 peptide
- Selected PCNA residues within 4 Å of P21 sidechains to define the interaction interface
- Chose 4–7 hotspot residues with sidechains oriented toward the ligand
- Removed P21 and exported the clean PCNA chain as a `.pdb` file for use as the RFdiffusion target

### 2. Binder Backbone Design (RFdiffusion)

- Ran RFdiffusion via Google Colab ([ColabDesign notebook](https://colab.research.google.com/github/sokrypton/ColabDesign/blob/main/rf/examples/diffusion.ipynb))
- Defined contigs specifying the target PCNA chain and binder length range (70–100 residues)
- Specified hotspot residues to direct diffusion toward the PIP-binding site
- Generated multiple backbone designs

### 3. Sequence Design (ProteinMPNN)

- Passed each generated backbone to ProteinMPNN within the same Colab notebook
- ProteinMPNN designed amino acid sequences compatible with each backbone geometry
- Set `num_recycles = 3` for AlphaFold validation

### 4. Structural Validation (AlphaFold)

- AlphaFold predicted the structure of each binder–PCNA pair
- Evaluated designs using:
  - **pLDDT**: per-residue confidence of the predicted binder structure
  - **i_PAE** (interface predicted aligned error): confidence in the relative position of binder and target at the interface
  - **i_PTM** (interface predicted TM-score): overall interface quality
- Applied threshold: **i_PAE < 11 Å** to identify credible binders

### 5. Orthogonal Validation

- Re-predicted shortlisted binders against the full trimeric PCNA complex using the AlphaFold Server
- Confirmed binding was retained at the PIP site and not displaced by other PCNA subunits
- Inspected each binder in PyMOL by aligning onto the 1AXC experimental structure to check for steric clashes with P21 and adjacent chains

---

## Tools and Resources

| Tool | Purpose |
|---|---|
| PyMOL | Structure visualisation, interface analysis, target preparation |
| RFdiffusion | De novo protein backbone generation |
| ProteinMPNN | Sequence design for generated backbones |
| AlphaFold 2/3 | Structure prediction and binder validation |
| Google Colab | RFdiffusion + ProteinMPNN pipeline execution |

---

## Key Concepts

**Conformational selection vs. induced fit** — relevant to understanding whether a designed binder stabilises a pre-existing PCNA conformation or reshapes the binding site upon contact.

**i_PAE as a filter** — interface PAE captures the uncertainty in the relative positions of binder and receptor residues at the interface, making it more informative than pLDDT alone for evaluating binding predictions.

**Hotspot-directed diffusion** — constraining RFdiffusion to specific interface residues focuses the generative process on the desired binding mode, reducing the search space and improving hit rate.

---

## Notes

This project was completed as part of the MSc in Computational Biomedicine coursework at the University of Southern Denmark (2025–2026). The pipeline follows standard practices in computational protein design; no novel algorithm development was undertaken.

---

## References

- Watson et al., *Nature* 2023 — RFdiffusion
- Dauparas et al., *Science* 2022 — ProteinMPNN  
- Jumper et al., *Nature* 2021 — AlphaFold 2
- Abramson et al., *Nature* 2024 — AlphaFold 3
- Gulbis et al., *Cell* 1996 — PCNA–p21 structure (PDB: 1AXC)
