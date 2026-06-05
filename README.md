# KS_analysis
Meta Analysis of KS across diverse geographical countries.  

# Status Zero: Before any Improvements

You have made incredible progress! The `Indiv_Country_script.Rmd` shows a massive amount of work, and identifying a core signature of 13 consistently dysregulated genes (including highly relevant angiogenic and metastatic drivers like NRP2, PODXL, and ITGB3) across such diverse geographical cohorts is a fantastic preliminary finding. 

Your draft findings and presentations provide a very solid foundation. However, before you fully commit to writing the manuscript, I strongly suggest implementing a few critical analytical steps. 

Right now, your Rmd script uses an **"intersection of independent analyses"** (often called vote-counting). While useful, it lacks the statistical power of a true meta-analysis, and it contradicts a key paragraph you wrote in your `My first PhD Paper.docx` draft.

Here is what I suggest you add or modify before finalizing the manuscript:

### 1. Perform a Unified "True" Meta-Analysis (ComBat-Seq)
In your `docx` draft (line 290), you wrote: *"The raw read counts for each cohort were integrated... followed by batch effects adjustment with the ComBat-seq algorithm."* 
However, your current `Indiv_Country_script.Rmd` does **not** do this. Instead, it analyzes USA, China, Tanzania, etc., separately and intersects the resulting gene lists.
* **Why it matters:** Intersecting individual lists requires a gene to pass the strict `padj < 0.05` and `|LFC| > 1` thresholds in *every single cohort independently*. This is extremely statistically conservative and will cause you to miss hundreds of biologically crucial genes that have moderate but consistent expression changes across all countries.
* **The Fix:** You need an R script that merges all individual count matrices into one massive matrix, uses `ComBat-seq` to remove the "Country/Study" batch effect, and then runs a single `DESeq2` analysis on the entire dataset. This will likely expand your 13-gene signature to a much richer, statistically robust gene set.

### 2. Viral Transcriptome Integration (KSHV)
Your PhD Concept and upstream bash scripts (like `alignment_quant.sh`) explicitly included dual-alignment to both the Human and KSHV genomes. 
* **The Fix:** The current Rmd only focuses on human gene expression. Your paper will be significantly more novel and impactful if you include the `kshv_counts.txt`. You can assess whether KSHV viral load or lytic vs. latent viral gene expression correlates with the expression of your 13 core human genes.

### 3. Weighted Gene Co-expression Network Analysis (WGCNA)
Your original PhD concept mentioned using WGCNA. 
* **Why it matters:** Standard DESeq2 treats every gene independently. WGCNA will cluster your thousands of genes into "modules" of highly correlated genes (e.g., an "angiogenesis module", an "immune exhaustion module"). You can then correlate these entire modules with clinical traits like Age, Gender, or Endemic vs. Epidemic KS. 

### 4. Upgrade from ORA to GSEA
In your docx, you correctly noted the limitations of Over-Representation Analysis (ORA) on a small list of 13 genes. 
* **The Fix:** Once you perform the unified ComBat-seq DESeq2 analysis (Step 1 above), you will have a full ranked list of all 20,000+ genes based on their log2FoldChange. Running GSEA on this full ranked list will provide a much more holistic view of pathway dysregulation than running ORA on just 13 genes.

### 5. Immune Deconvolution (e.g., CIBERSORTx)
Bulk RNA-seq of KS lesions is heavily confounded by the proportion of infiltrating immune cells versus endothelial spindle cells. 
* **The Fix:** Running an algorithm like CIBERSORTx or EPIC on your normalized count data will estimate the fraction of CD8+ T-cells, Macrophages, etc., in each sample. Comparing these immune fractions between Endemic (African) vs. Epidemic (USA/HIV+) KS would be a highly publishable figure on its own.

### How to Proceed?
If you agree with these suggestions, **I can write the next R script for you**. I can create a new script (e.g., `Unified_Meta_Analysis.Rmd`) that takes all your individual count files, merges them, runs `ComBat-seq` for batch correction, and performs the unified `DESeq2` and `GSEA` analyses. 

Would you like me to draft that unified meta-analysis script?
