---
layout: post
title: "Computational Linguistics Examination Revision"
description: "All of the notes for the course"
category: academia
tags: [reviews, research, examination, revision, notes]
---
{% include JB/setup %}
#Computational Linguistics

These are examination preparation notes from the slides for a course I took at the University of Saarland in Saarbrücken in the summer of 2012. The majority of the information here came from the slides for that course, which are available online, but not under any copyright. To save embarassment in case I mistype or misunderstood, I won't cite the lecturers here. Much of this information can be found in better quality in Dan Jurafsky or Chris Mannings books, cited below. 

That is all, I think. 

---
##Lecture 1: _Course Outline_

Textbooks:  

 * Dan Jurafsky and J. H. Martin: Speech and Language Processing: An Introduction to Natural Language Processing, Computational Linguistics, and Speech Recognition, Prentice-Hall, 2009.  
 * Chris Manning and H. Schütze: Foundations of Statistical Natural Language Processing, MIT Press, 1999.  

#####Finite State Automata (Lecture 1)  
 * Deterministic & non-deterministic finite state automata
 * Algorithms for recognizing strings
 * Aogrithms for determinizing non-deterministic automata

#####Grammars and Parsing (Lecture 2,3,4)
 * Context free grammars
 * Bottom-up parsing (Shift-reduce)
 * Chart parsing (CYK)
 * Probabilistic grammars and parsing
 * Dependency parsing

#####Distributional Semantics (Lecture 5)
 * Distributional semantics
 * Vector space models
 * Similarity measures

#####Matrix Factorization (Lecture 6)
 * Latent semantic analysis (LSA)
 * Non-negative matrix factorization (NMF)

#####Clustering (Lecture 7)

#####Word Sense Disambiguation (Lecture 8)

#####Computational Semantics (Lecture 9)
 * Semantics construction
 * Scope underspecification
 * Dominance graphs and tree automata

#####Combining Visual and Textual Data (Lecture 10)

---
##Lecture 2: _Finite State Automata_
Due to worries about copyright for these slides, I will not be posting images or screenshots from them, unfortunately. 


####Basic Definitions
 * An __alphabet ∑__ is a finite set of symbols
 * A __string over ∑__ is a sequence of symvols from ∑. 
 * __epsilon__ is the empty string. 
 * The __length |w|__ is the number of symbols in w. 
 * __∑*__ denotes this set of all strings over ∑
 * A __formal language__ is a subset of ∑* for some alphabet ∑
 * A __configuration__ is a pair {k,w} which is an element of K•∑*, where k is the current state, and w is the unread part of the string to be processed. This is basically where one is in the automata. 
 * __Yields in one step__ means when {k,w} ⊢m {k',w'} iff w = aw' and ∂(k,a) = k'.
 * __Yields__ ⊢m* is the reflexive, transitive closure of ⊢m. (_What this means, I don't rightly know.)
 * A language is __accepted__ if the unread string after going through the machine consists only of epsilon, and the current state is an element of F, the final state. Basically, L(M) = {w | {s,w}⊢m*{k,epsilon} for some k element F}.  In which case, the entire string ∑ is a subset of the language applied to the machine. 
 
###Deterministic Finite Automata
 * M = {K, ∑, ∂, s, F}
 * * K is a finite set of states (a list)
 * * ∑ is an input alphabet (a list)
 * * ∂ is a transition function. ∂(k, a) means that when the machine M is in state k and reads input a, it goes into state q'. This is generally a list of several transition functions, the the first state (for instance, q_0) and then an input _a_ from the alphabet ∑. This then is mapped (or equals) another state. A loop would be the same state. 
 * * s element K is the start state
 * * F subset K is the set of final (accepting) states 

#####Automata as Graphs
To understand automata, you must know that: nodes are states, edges represent the transition function, > marks the start state, and double circles are final states.

#####Recognition Algorithm Pseudo-code

	function RECOGNIZE(DFA M, STRING input)
		index ‹- 0
		state ‹- start state of M
		while index < length(input) do
			state ‹- trans[state, input[index]]
		end
		if state is a final state of M
		then return accept
		else return reject
	end
	
###Nondeterministic Automata

In these, several symbols (or none) can be read at once, and there are several possible next states. M = {K, ∑, ∆, s, F}, where ∆ is a subset of K•∑*•K and is a finite transition relation. For DFAs, the next step is always determined - for NFAs, the next state can go either way. 

The only real difference seems to be that k' is included in the original tuple of the transition function, and where there are several options for each transition on occasion. While running, each time there is an accepted string, that is added to the agenda. The configuration continues to run on the first item in the agenda until it returns an empty string (after the string is accepted or rejected). It then goes on to take the next item in the agenda, and so on. 

For every Nondeterministic automata, there is an equivalent Deterministic finite automata. It doesn't work the other way around as easily. In NFAs, there is more than one succesful combination which can get to the final state. To implement, use backtracking - if there is a failure, go back to the point you remember last that workd, and use that (on the agenda).

#####Recognition Algorithm Pseudo-code

	function RECOGNIZE(NFA M, STRING input)
		while agenda is not empty do
			else
					PUSH(agenda, conf’)
			end
	end

#####Subset construction algorithm

 * __epsilon-closure(s)__ returns the set of NFA states reachable from state _s_ using Element-transitions  
 * __epsilon-closure(T)__ returns the set of NFA states reachable from some _s_ in _T_ using element-transition
 * __move(T,a)__ returns the set of NFA states to which there is transition for input _a_ Element ∑ from some state _s_ element _T_

---
##Lecture 3: Parsing

Grammars generate sentences (“words”). Context-free grammars can be thought of as G = {N, T, R, S}, where:

 * N is the nonterminal symbols  
 * T is all terminal symbols
 * S (an element of N) is the start symbol
 * R is the finite set of production rules, and is a subset of N•(N•T)*
 
In this case, if [x,y,u,v,w,z] is our alphabet (the union of nonterminal symbols and terminal symbols), X =>G y in our grammar iff x = uvw and y = uzw, and v->z is one of our production rules (is an element of R). A language generated by this grammar is accepted if {w|S =>G* w}, where =>G* is the reflexive, transitive closure of =>G. This works from the left edge of y  - If there is a rule S -> NP VP, then the next =>G application of the grammar G would go to DET N VP (if there is a rule such that NP -> DET, for instance). In all cases, end in the terminal symbol, and then move on to the next element to the right of =>G (in y). Although, according to these slides, it doesn't matter if you stay consistent with which edge to start from, or which rules to apply where. Leftmost or rightmost derivation is certainly the norm, if not necessary, however. Either should end with the same parse tree. 

The language generated by a grammar G is the set of all of those sequences that can be derived from the start symbol. In this case, derivation is done when you replace one symbol or sequence by a sequence of symbols. 

The derivations themselves are uninteresting - what is interesting is the __parse trees__. Each node (inner nodes elements in N, leaf nodes elements of T or empty symbols)and its branches correspond to one rule in the grammar. Epsilon is accepted only as the only child of a node. 

If w = w1…wn has two or more parse trees, than the grammar is ambiguous. 
 
####Parsing Strategies
__Top-down parsers__ work by starting with S, and then applying from left to right (like in natural language processing), and trie to match the input sequence. __Bottom-up parsers__ do the opposite, and start with the leaf nodes, going up, looking at subsequences in the input that match the left-hand side of a rule and apply it from right to left. 

__Shift-Reduce Parsing__ is a bottom-up strategy, with a stack and input {[stack],[input in the format w1…1n]}. In each step, there are two options: __shift__ the left-most symbol to the right edge of the stack, or __reduce__ by applying a matching rule to the topmost elements (right-edge, two or more) on the stack. For unambiguous grammars, the shift-reduce decision (which to do when) is automatic. 

Bottom up parsers, however, can't deal with some grammars, for instance with trace elements (where the leaf node is an empty symbol), of for cylic rules, or for ambiguous prepositional phrases such as _The dog killed the man in the park with a telescope_. 

A context-free grammar doesn't depend on context to know whether a rule applies or not. One way to get around this problem is to use __chart-parsing__, which stores intermediate results for already analysed constituents in a chart, a compact representation of all possible analyses. Charts (“parse forests”) can contain complete consituents or hypotheses for possible constituents. 

__CYK Parser__ (Cocke-Younger-Kasami) is a bottom-up chart-based parser. This applies only to grammars in Chomsky normal form, where A -> w (terminal symbol), A -> B C (both non-terminal symbols), and S -> epsilon (S start symbol, only if epsilon is an element of the language). 

	function CYK(G, w1 ... wn): 
		for i in 1 ... n do

The CYK Parser is correct if the sentence is an element in the ordered list of leaf nodes, and complete if S can be derived for each input word. The runtime for this algorithm is very fast - polynomial in the input length O(n^3). However, it requires the Chomsky Normal form, which can be slow due to the size. On the plus side, all parse trees can be saved by storing nodes as elements in an array instead of as booleans, by storing for each category A a list of pointers to other entries in the chart that were used to derive A. 

This can be done by binarizing the output, either using left or right binarization. 

	left binarization(G):

There are implementation variations of this:  

 * Method 1: iterate over all rules A -> B C, and check if B and C are next to each other in the T
 * Method 2: iterate over B leaf nodes, then over all A -> B C, and see if C is to the right side of B in T
 * Method 3: iterate over C leaf nodes, then over all A -> B C, and see if A is to the left side of C in T
 * Method 4: iterate over all B and C leaf nodes, and then see if A -> B C exists. 
 
According to Song et. Al, 2008, in EMNLP, right binarization on method 3 is the most efficient, while for left binarization method 2 is the most efficient. 

---
##Lecture 4: _Clustering_

The goal of clusting is to group similar items together. To do this, define similarity between samples, define a loss function, and then find an algorithm that minimizes this loss function. These can be used to cluster text, words, images, image regions, and so on.

In __K-means cluster__, when there are no labels to go by, one has to go on the assumption that similar objects will be close to each other in a spatial relation. So, the aim is the find _k_ number of clusters that will classify the data properly. We have to manually supply _k_. So, we randomly assing _k_ number of inital centroids (“mu”), which we will assume to be the center of clusters. We then check _mu_ against the other points using distance measurements, and then assign each point to an imaginary cluster based on these measurements. Then find the meand distance of all of the points of the same cluster, set that as _mu_, and recalculate. Eventually, there should be convergance (optimal clustering). 

Distortion, the sum of the squared distances between each point and it's centroid, should be lessened the tighter a cluster holds together. This levels out after a few iterations. Picking k such that distortion is as small as possible is a good idea - one way to do this is to find a large jump in the value of j for a given amount of Ks - the larger the jump, the more likely that _k_ was the right value. If the number of clusters (k) is equal to the number of elements (n), then the loss function will be 0, but of course, this is not what we want. We want to minimize the number of clusters.

__Soft clustering__ involves no strict assignment of any point _n_ to a cluster. There can be overlapping regions, no class information whatsoever, or soft clustering based on probabilities. There can also be hierarchical clustering, either agglomerative (making clusters from the unclustered data) or divisive (making more clusters from previously clustered data). For text clustering, several options are: word frequency, TF-IDF, stop wording, and stemming. What these last two are is anyone's guess. 

The __Brown algorithm__ clusters words together with similar neighbors, and minimizes perplexity in the training test. This is useful for NE tagging. (For instance, it would identify city names based on prepositional collocations.) 

	start with some initial mapping w -> gw
		for w in vocabulary:
			for each class k:
				tentatively exchange word w from class gw to class k and update counts
				compare perplexity for this exchange
			exchange word w from class gw to class k with minimum perplexity
	do until stopping criterion met 


#####Similarity Measures

 * Euclidean distance: pythagorean theorem, in essence.
 * Manhattan disance: D1(x,y) = ∑|x_k - y_k|. The sum of the absolute value of the x value for each point _k_ minus the y value for said point. Basically the sum of the two distances, or going the long way around a triangle.
 * Maximum distance: d_inf(x,y) = max_k(|x_k-y_k|). Basically, the maximum possible distance between two points. 
 
 Others include: Cosine, edit distance, Jaccard, kernels. A _distance measure_ is a function from pairs of points to reals such that: 
 
 * d(x,y) ≥ 0 [The distance is greater than zero]  
 * d(x,y) = 0 iff x=y (If the points are the same, there is no distance)
 * d(x,y) = d(y,x) (The points should be reversable with the same distance value)
 * d(x,y) ≤ d(x,z) + d(z,y) (A point further on on either axis will make a longer distance - triangle inequality)
 
---
##Lecture 5,6,7: _Latent Spaces and Matrix Factorisation_
####Lecture 5
The goal is to treat document and word clustering on the same level, and to find low-dimensional representations that can find latent similarities. For document clustering, one can describe each document by a vector containing the frequency of the words. For word clustering, antoher vector containing the frequencies of each word in different documents can be used. These can be added together into a single matrix - the tf-idf measurement.

On the subject of matrices: A matrix is an array with two indices. The transpose is when the indices are swapped. The elements of a product matrix (the product of two matrices) can be calculated by multiplying each against the other like so: c[i,j] = a[i,k]*b[k,j]. When a unit matrix - with 1s on the diagonals, and 0s else, represented as a_i,j = d_i,j - can be used to diagnosed whether a product matrix is a result of two inverse square matrices. 

---
####Lecture 6

#####Latent semantic analysis (LSA)
__Latent semantic analysis (LSA)__ is an NLP technique for finding relationshops between a set of documents and the terms therein, by producing concepts related to both which aren't on the surface analysis level (hence, latent). It works on collocations - words that are close in meaning will occur in similar texts. __Singular Value Decomposition (SVD)__ is used to reduce the number of columns while preserving similarity structure among rows (or vice versa). Words are then compared by taking the cosine of the angle between the two vector of any rows. Values close to 1 represent very similar words, while values close to 0 represent very dissimilar words. 

If we have a matrix A, we decompose that such that Ã = TSD^t - in essence, into three matrices, one of which is a transpose. Here, it is best (although I don't know why) to keep |Ã-A|^2 minimal. A is a t•d matrix, T a t•n matrix, S and n•n matrix, and D a d•n matrix. In essence, then, T and D are orthogonal matrices, and S is the diagonal matrix. Document-document similarity can be found by rewriting ÃtA as = (SDt)tSDt. Term term similarity is (TS)(TS)t. 

LSA performance consistently improves recall on standard test collections (precision/recall improved). It has variable performance on larger TREC collections. The dimensionality of the latent space is hard to asses, but 300-1000 seems to work best. The computational cost is high.

#####Probabilistic Latent Semantic Analysis
Instead of using decompositionality into orthogonal vectors, PLSA works using statistically. The probability of a document is the product of the probability of all terms in that document (uncontroversial). A term frequency matrix would add the sum of the probabilities of a term in a document, for each document.  By introducing a hidden topic in a lower-dimensional space, and then figuring out the probability of a term given that topic times the probability of that topic in a document, the likelihood of the document can be reassessed. P(term|doc)=∑P(t|k)P(k|doc). And the likelihood of the document is then P(doc)=∏{∑P(t|k)P(k|doc)}^A(t,doc). There is a training objective function which maximises with respect to the parameters p(t|k) and p(k|d). 

#####Non-negative Matrix Factorisation
NMF works by decomposing a non-negative matrix A into two matrices, W and H. A=WH, then. A: N•M (data matrix), W: N•R (source matrix), and H: R•M (mixture matrix). NMF is equivalent to a relaxed form of K-means clustering: matrix factor W contains cluster centroids and H contains cluster membership indicators, when using the least square as NMF objective. This provides theoretical foundation for using NMF for data clustering. This matrix is factored into a term-feature and a feature-document matrix. The features are derived from the contents of the documents, and the feature-document matrix describes data clusters of related documents. 

In essence, when multiplying matrices the factor matrices can be of significantly lower rank than the product matrix and it's this property that forms the basis of NMF. If we can factorize a matrix into factors of significantly lower rank than the original matrix then the column vectors of the first factor matrix can be considered as spanning vectors of the vector space defined by the original matrix.

NMF is more easily lent to probabilistic approaches than LSA.

Some metrics: 

 * Kullback-Leiber-Divergence: D(A|WH)  
 * Frobenius Norm: .5|A-WH|ˆ2  

---
####Lecture 7

Nothing of interest.

---
##Lecture 8: _Probabilistic Parsing_

To find the most probable derivation tree when a sentence is ambiguous, we need a probabilistic model of context-free grammar, and methods to estimate probabilities. There are some motivations for this: natural language is ambiguous, it would be useful to be able to automatically induce grammars, efficient search would use the most likely tree first, and robustness. A __probabilistic context-free grammar (PCFG)__ has a context free grammar {V,∑,R,S}, and a function P which assinges a probability value between 0 and 1 to each rule P(A -> B), such that P is the probability of A expanding to B. There are other ways of notating this. 

The probability of a derivation tree T is the product of the probabilities of all the rules that have been used to expand the nodes in T. The probability of a sentence w is the sum of the probabilities of all of its derivation trees. A PCFG grammar is consistent if ∑_(w element of L(G))P(w)=1. Recursion breaks this, and leads to inconsistent grammars. A Catalan number is the number of trees possible for a string of a^(i+1) length (the number = i).

Language modelling using probabilistic parsing (“inside probabilities”) is done by computing the probability that w is derived from S for for an input by computing the sum of the joint probabilities of w and T. Probabilisitic parsing (“viterbi scores”) compute the most likely derivation tree for w by finding the argmax tree. 

The probability of a subtree is independent of the context in which it occurs, and the nodes which dominate it. It is possible to extend the CYK parser to include the inside probabilities for trees for all substrings because of this. T[i,j,A] is the probability of the most likely derivation, while B[i,j,A] is the corresponding derivation tree.

	function CYK(G, w1 ... wn): 
	  ⟨initialize T and B⟩
		  T[i-1, i, A] = P(A → wi)

For learing PCFG probabilities, there are two options: either count the frequencies of rules in a syntactically annotated treebank, or use the inside-outside algorithm. For the first, we can construct a grammare from the treebank by identifying the rules with all subtrees of height 1. The probability of a rule is just the number of times that rule has been used, over all rules used in all trees of the corpus. So, the probability of the sentence would then be the product of these rules. 

To evaluate, coverage and accuracy are metrics. Coverage is how many sentences are well-formed according to the grammar. Accuracy is how many sentences are correctly parsed, with regards to the relative correctness of the category label, start and end position (yeild) of all consituents (subtrees). Labelled precision is the percentage of all correct subtrees in the output, while labelled recall is the percentage of all correct subtrees which are in the gold standard. 

The problem with subtree probabilities is that we want to capture lexical and structural dependencies. Lexical dependencies, for instance, are when a verb takes a certain prepositional phrase as its argument. These can be lexicalised by knowing which verbs take which rules. Parent annotation can be used for lexical entries, such as S_(dumped), which would then account more readily for arguments. This leads to much larger grammars and it is harder to estimate the rule probabilities. The complexity causes the runtime to be O(|rules|n^3), and at worst cast |rules| = |nonterminals|^3. 

Structural dependencies are when, for instance, subject-NPs are more likely to have pronouns (which need a different terminal rule NP -> PN instead of NP -> Det Noun) than object-NPs. A way to get around these is to build in parent-annotation such as NP-SBJ. Nodes are then annotated with the label of their parent nodes. This is similar to conditional probabilities, where P(NP^S -> PRP) is the same as P(NP -> PRP|S). Parent annotation can be useful for preterminal nodes, as well. The Penn Treebank makes no distinction beween different sorts of conjunctions, prepositions, or complementizers - these can be ironed out using parent annotation. The drawbacks are that the grammar gets larger, you need more training data, and it is more likely to overfit. For runtime, the worst case is |rules| = |nonterminals|^3•|terminals|^2, where |terminals| are much larger than |nonterminals|, and the runtime is reduced to O(n^5). 

---
##Lecture 9: _Dependency-based Parsing_

For referencing:

 * an ESSLLI 2007 course by Joakim Nivre and Ryan McDonald 
 * an ACL-COLING tutorial by Joakim Nivre and Sandra Kübler
 * Sandra Kübler, Ryan McDonald and Joakim Nivre (2009). Dependency Parsing.
 * Joakim Nivre (2008). Algorithms for Deterministic Incremental Dependency Parsing. Computational Linguistics 34(4), 513–553.

The absic idea of dependency trees is that syntactic structure equals lexical items linked by relations. They are usually trees, but they don't have to be. The relation is H (head) -> D (dependent). Parsers are easy to implement and evaluate, and are suitable for free word order languages, and are close to predicate argument structure (often enough). They are clear for subject, object, and so on, but less clear for complex verb groups, subordinate clauses, coordination, and other complex constructions. 

Criteria for dependency relations between H, D, and C (linguistic construction):

 * H determines the syntactic category of C; H can replace C.  
 * H determines the semantic category of C; D specifies H.
 * H is obligatory; D may be optional.
 * H selects D and determines whether D is obligatory.
 * The form of D depends on H (agreement).
 * The linear position of D is specified with reference to H.  
 
A dependency graph G = {V, A, L, <}, where:

 * V = a set of vertices (nodes)
 * A = a set of arcs (directed edges)
 * L = a set of edge labels
 * < = a linear order on V. 
 
Formal conditions on dependency graphs:
 
 * G is weakly connected. 
 * G is acyclic.
 * Every node in G has at most one head.
 * G is projective.

A dependency graph G is projective iff if wi -> wj, then wi ->* wk for all wi <wk < wj, or wj < wk < wi, and if wi is the head of wj, then there must be a directed path between wi to wk, for all wk between wi and wj. We need non-projectivity to deal with long distance dependencies and free word order, where the arcs intersect. The amount of projectivity differs between languages. 

Dependency based parsing is grammar-based and data-driven, based on transitions or graphs. For transition based parsing, there is a configuration C = {S,Q,A}, where S is a stack of partially processed tokens (nodes), Q is a queue of unprocessed input tokens, and A is a set of dependency arcs. The initial configuration would then be {[w0], [w1…wn], []}, where w0 is the root. A terminal configuration that is accepting would be {…, [], …}, with nothing left in the unprocessed tokens. Both Left-Arc and Right-Arc operations can be performed. A Left-Arc operation adds a dependency arc (wj,r,wi) to the arc set A, where wi is the word on top of the stack and wj is the first word in the buffer, and pops the stack. Right arc adds a dependency arc (wi, r, wj) to the arc set A, where wi is the word on top of the stack and wj is the first word in the buffer, and then pops the stock and replaces wj by wi at the head of the buffer. Shift is also an oeration, moving the top word of Q to the right-edge of the stack. It is normal to do all left-arc first, and then all right-arcs left in the stack. 

For deterministic parsing, oracle(c) predicts the next transition. Parse replaces the terminal nodes in some way (I don't know C notation used here, so I'm not sure how). DP has a linear time complexity: the algorithm terminates after 2n steps for input sentences with n words (as it does it both ways). The algorithm is complete and correct for the class of projective dependency trees if for every projective dependency tree T there is a sequence of transitions that generate T, and if every sequence of transition steps generates a projective dependency tree. A way to approximate the orcal is to represent configurations by feature vectors; for instance, the lexical properties, cateogory, or labels of partial dependency trees. (I believe, I'm not sure.)

For non-projective parsing, C = {L1,L2, Q, A}, where L1 and L2 are partially processed nodes, Q is the unprocessed input tokens, and A is a set of dependency arcs (as before). Thus, the initial configuration is {[w0],[],[w1…wn],{}}, where w0 = ROOT. The accepting terminal configuration is {[wo,…,wn],[],[],A}. As well as left arc, right arc, and shift, No-Arc is defined as {[…,w],[…],[…],A}, which returns {[…],[wi,…],[…],A}. L1•L2 would be the concatination. This algorithm is sound and complete for the class of dependency forests. The time complexity is O(nˆ2), with at most n Shift-transitions (to the end of the string), and between the ith and i+1th Shift transitions there are at most i transitions (left-arc, no-arc, and right-arc).

Graph-based parsing is also possible, where the baisc idea is that there is a complete graph where the nodes are the words from the input and edges are annotated with scores. Parsing would then be computing the maximum spanning tree. 

---
##Lecture 10, 11: _Algorithms for Scope Underspecification_

####Lecture 10
Dominance graphs are ways of modelling scope ambiguities, and there is an algorithm for solving these. __Truth-functional interpretation__: The meaning of a declarative sentence is given by its truth conditions. We can represent the meaning of natural language sentences by logical formulae which 'capture' the truth conditions of the original sentence. _Compositionality_: The meaning of a complex expression is a function of the meaning of its parts and of the syntactic rules by which they are combined. The compositional semantic construction is based on the syntactic tree of the natural language expression: the semantic lexicon assigns meaning representations to the lexical (leaf) nodes of the syntax tree, and the semantic representation of an inner node is computed by combining the representations of its child nodes. Every syntax tree is then mapped to a unique semantic representation. However, as always, there are lexical (_Iraqi head seeks arms_) and structural ambiguities (_Every student eats an apple_). Scope ambiguities arise when a sentence contains two or more quantifiers or other scope-taking operations (negations, modals, etc.) 

There is a __combinatorial explosion of readings__ in that the number of readings of a sentence can grow exponentially in the number of scope-taking operators it contains. The way to deal with this are to ignore scopre ambiguities, enumerate all readings and select the right one (needs more complex semantics construction rules and a method to choose, as well as being computationally expensive), or to use __scope underspecification__. This doesn't explicitely enumerate readings, but represents all readings of a sentence by a single compact underspecified representation (USR), from which all individual readings can be enumerated, and on which we can perform inferences directly on the level of underspecified representations. 

Each reading can be represented as a binary tree. Dominance graphs are another way of doing this, with __tree fragments__, specifying the semantic material common to all readings, connected by __dominance edges__, which specify constraints as the upper node must dominate the lower one. Dominance edges in __normal dominance graphs (NDG)__ always go from leaves (holes) to roots. An NDG is a graph G = {V, E u D}, where the subgraph {V,E} is a collection of node disjoint trees where the height of each tree is ≤ 1, if {v1,v2} are elements in D, then v1 is a hole and v2 a root, and where every hole has at least one outgoing dominance edge. __Labelled dominance graphs__ are the same, with L a labelling function that assigns a node _v_ a label with arity _n_ iff _v_ is a root with _n_ outgoing tree edges. A dominance graph G is in __solved form__ if G is a forest. Whenever v1 and v2 are connected by some dominance edge in G', there must be a directed path from v1 to v2 in G. Dominance graphs and their solved forms differ only in their sets of dominance edges, and solved forms of connected dominance graphs are always trees. 

There are two problems that need to be attended to: __the solvability problem__, which askes if a given dominance graph has any solved forms, and the __enumeration problem__, where given a dominance graph, all (minimal) solved forms must be enumerated. The algorithm to deal with these comes from Bodirsky &al. (2004), and constructs a solved from of a dominance graph G such that:

 1. nondetermininistically choose a “free fragment” F from G
 2. remove F from G; this decmoposes the graph G into weakly connected components G1…Gk
 3. recursively compute a solved form for G1…Gk
 4. attach the solved from of G1 under the corresponding hole of the free fragment F (for 1 ≤ i ≤k)
 
A fragment F is __free__ in a  NDG G iff the root has no incoming dominance edges, and no distinct holes of F are connected by an undirected path in the graph G'obtainedfrom G by removing the root of F. If G is a solvable (normal) dominance graph, then F is a free fragment in G, which is equivalent to G has a solved from with top-most fragment F. 

A slightly simplified version working only for connected NDGs would be:

	solve(G = ⟨V, E ⊎ D⟩) = (*)

The following are equivalent: solve(g) fails for some nondeterministic choice, G is not solvable, solve(g) fails for all nondeterministic choices. The runtimme for the solver is O(N+M), where N is the number of nodes and M is the number of edges in a dominance graph G. The overall runnint time of solve(G) is in O(N(N+M)) per solved form. 

Extra reading:

 * Alexander Koller, Manfred Pinkal, and Stefan Thater. Scope Underspecification with Tree Descriptions: Theory and Practice. In: Resource Adaptive Cognitive Processes. Ed. by Matthew Crocker and Jörg Siekmann. Cognitive Technologies Series. Berlin: Springer. 2009.

---
####Lecture 11
A chart based solver can be used to figure out NDGs. The idea is to use dynamic programming techniques and store intermediate results in a chart-like datastructure. The chart records how graphs are decomposed into smaller subgraphs if free fragments are removed. The chart assignes each subgraph a 'split', which consists of references to a free fragment F and the weakly connected components of G/F. The notation for this is F(G1…Gn). The complexity for this is comlex (meaning, I don't understand it, and I don't want to write it down at the moment.)

######Hypernormally connected dominance graphs

We are normally interested only in solved forms in which every hole is related to exactly one root (such forms can be called _simple_, here). These can be mapped to _proper_ trees simply by _plugging_ the hole with the root connected to it by a dominance edge. However, not all problems are simple. So, we need to identify a class of dominance graphs that onle have simple solved forms - these are known as hypernormally connected dominance graphs. 

A __hypernormal path__ in a normal dominance graph G is a path in the undirected version of G that does not use two dominance edges incident to the same hole. A normal dominance graph G is __hypernormally connected__ if each pair of nodes is connected by some hypernormal path. __lemma__: if G is a hypernormally connected normal dominance graph with free fragment F, then all WCCs of G/F are hypernormally connected. __proposition:__ if a normal dominance graph is hypernormally connected, then all its (minimal) solved forms are simple. 

#####Tree Automata
A tree automaton is a tuple A = (Q, ∑, Qf, ∆), where:  

 * ∑ is a finite ranked signature  
 * Q is a finite set of states  
 * Qf is a subset of q with final accepting states  
 * ∆ is a finaite set of transition rules  

Some transition rules: F(q1(x1)...qn(xn)) -> q(f(x1...xn)), where f is an element in ∑, q...qn are elements in Q, and x1...xn are different variables.  

A tree is accepted by an automaton if t -> q(t), and q is an element in Qf. The language L(A) of trees recognized by A is the set of trees accepted by A. 

Basically, charts are tree automata, or can be translated into them, provided that the original dominance graph is hypernormally connected. The class of recognizable languages is closed under union, complement, intersection. We can model certain inferences on the level of dominance charts by intersecting regular tree languages using redundancy elimination and weakest readings. 

Redundancy elimination means that if we have two ambiguous readings that are logically equivalent, we can model the relation between them by rewrite rules, translating these into a tree automaton, and then using redundancy elimination which is the intersection of regular tree languages. 

---
##Lecture 12: _Distributional Semantics_

The goal of distributional semantics is to treat document clustering and word clustering on the same footing (the same semantic space), by finding low dimensional representations. Using vector space models of semantics can do this. The advantages are that we can derive semantics from a corpus, and it performs well on tasks that need to measure semantic similarity for words, phrases, and documents. To do this: build a term-document matrix, calculate the distance between vectors representing documents directly, then use LSA. You could also use a word-context matrix. Similarity of relations an be used, as well, by using pairs of words and representing how the patterns establish relations between words, and then measuring the similarity of these patterns. 

Regarding vector-based models of Semantic composition, the meaning of larger units is determined from the meaning of smaller units (compositionality). One way to model this is to use a logic-based view, where one writes down logical expressions for parts, and the logical expressions fro larger units derived from those parts. This is limited by idiomatic or non-compositional multiword expressions. Compositionality can be formalised thusly: P = f(u, v, R, K), where:  

 * u: representation of the meaning of the first constituent  
 * v: representation of the meaning of the second constituent  
 * R: syntactic relation  
 * K: knowledge about the real world  
 * p: meaning of the composition  

A vector-based approach to this would realise that p, u, and v are vectors in some semantic space. There are linear functions which can be used to analyse the matrix to find rleations: the most general is p = Au + Bv + n, where A and B are matrices. Specific verions of this are additive, Kintsch, and weighted additive. Bilinear multiplicative combination would use p = Cuv, where C is a rank 3 Tensor. At this point, I am starting to wonder if I am sane. I believe I am no longer sane. Tenser, tenser, said the tensor. That is, pk = the sum of c_(k,i,j)u_iv_j. Particular versions of this are multiplication and circular convention. 

Mitchell and Lapata did collected human similarity ratings for adjective-noun, noun-noun, and verb-object phrases. Against these, multiplicative works best, but a theoretical foundation is needed (we don't know why, and we're certainly not going to tell you.) 

Summary: the playground of distributional semantics. Compositionality. (Again, this is an entire slide. And on that note, I am sliding out of here, as this was the last set of slides.) 

---
##Lecture 13: _Words and Images_

Correlation concept/region is stronger than color/texture. 

Regional mutial information: the label of a region (grid on an image) and a concept label (face, vehicle, etc) can be found by summing the discrete represenation of color, texture, and edge information. Or something similar.

Regional Bayes Classifier; use one model per region, such that P(v1…v_r|concept) is the product P_r(v_r|c). So, the probability of certain visterm (discrete representation of color, texture, and edge information) given a concept is the product of the probability with respect to the region of that visterm given the concept. This can be applied to absolute discounting. Smoothing methods include linear interpolation (Jelinek-Mercer Smoothing), with a Dirichlet Prior. The background distribution can be found using a uniform distribution (zerogram), where the probability of visterm given a concept is equal to 1 over the absolute value of the number of visterms. The frequency of visterms (unigram) can be found by looking at the probability of a visterm given a concept, which is equal to N(v) over the sum of N(v). Regional information gives significant improvement. 

Since that doesn't make much sense, in summation, one can find patterns by analysis of regional mutual information, region specific modeling is important, the best variants to use are linear interpolation and backing off to background unigram, and this is an extremely simple method of picture identification. 

To generate captions, annotate each image with key words (if scraping the web) by combing the surrounding text, then choose a suitable sentence fromt the text by measuring similarity between sentence and key words describing the image sing word overlap, cosine, and KL-divergence. An abstractive approach would be to pikc the most likely word sequence given the key words by estimating an n-gram based on the surrounding text, keywords, and then combine the two using LM adaption techniques. Including a phrase based constraint can help. In summation, key words and images // caption generation. (That last sentence is a slide. No, seriously. And that's the course.)









