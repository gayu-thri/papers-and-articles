## SlimPajama

### Abstract
- Data combinations: web text, wikipedia, github, books
- De-deuplicated multi-source dataset (1.2T tokens -> Refined to 627B tokens)
- Research name = SlimPajama-DC
- Observations
	1. Global deduplication vs. local deduplication
			- Global = Across diff. sources; Local = Within single source
	2. Proportions of high-quality/highly-deduplicated multi-source datasets
- To study above,
	- Constructed 6 configs of SlimPajama
			- Train on 1.3B Cerebras-GPT model with
- Discoveries
	- Best config >>> 1.3B model trained on RedPajama (same no. of training tokens)
	- 1. Global De-duplication; 2. Increasing Data Diversity on 7B with >Batch size
- Advantages of de-duplication in general
	- Model isn't repeatedly exposed to same/similar data points - Efficient training
	- Redundant data - Slow convergence, Model overfiting.	
### In this paper
- Primary Areas in Research:
	1. Global-level & Local-level deduplication
	2. Efficacy of various combinations of thoroughly deduplicated datasets

	Reasons for 1 & 2:
	1. All sources data - No cross-domain overlaps inside
	2. To manage integ' & proportion of diverse domains

### Deduplication Concepts Explained

- **Global vs Local De-duplication**:
	- **Global**: Removes from entire combined datasets (Overlaps across sources)
	- **Local**: Removes within each individual source dataset before merging

	- In most open-source LLM training data - only local de-duplication is performed - NEGLECTING REDUDUNDANCY ACROSS DIFFERENT SOURCES

	- **CONS OF GLOBAL**: More hardware memory is naturally required by this global strategy

- **Different combinations of highly de-duplicated datasets**:
	- More likely to generalize well across various tasks
	- Data exposed to wider range of following:
		1. Vocab, syntax, semantics
		2. Cultures, beliefs, demographics
		3. Balanced and less prone to biases

- **Specialization vs Generalization Trade-off**:
	- Combining many specialised datsets -> Jack-of-all-trades model
		- While model can tackle wide range of tasks; Can't match in depth understanding like a specialised model for a particular domain


### SlimPajama Dataset Details

![Data source proportions for various datasets](images/dataset_proportions.png)

- Dataset Token Frequencies
	- Different datasets often highlight varied types of tokens
		- Example: GitHub prioritizing code & arXiv centering on academic content
	- KL divergence between 2 domain distributions taken
		- ??? (Read later)

### Overview of Pipeline

- SlimPajama Preprocessing pipeline
	- ![](images/slimpajama_pipeline.png)

1. Low-length document filtering
	- Percentage of doc low-length filter rate: 1.86%
	- Additional global filtering: Remove short (<200 characters), low-quality documents
2. Global de-deduplication
	- Percentage of data source bytes duplication rate: 49.6%
	- Most significant duplication found in CC & GitHub
	- Removes duplicates both within & between each data source
	- Algorithm used: **MinHashLSH**
		- Jaccard similarity thresold = 0.8
		- Document signatures constructured - preprocessed lowercase 13-grams
		- **NOTE**: Vanilla implementation of MinHashLash doesn't scale to 1T token datasets like RedPajama without **running out of memory**
			- Solution: 
				- Optimizing memory usage; 
				- Parallelization to perform deduplication on 64 CPU cores with 1.4TB peak memory
				- Easily decreased by creating multiple MinHashLSH objects to query

### Pipeline in detail

#### Step 1: 


