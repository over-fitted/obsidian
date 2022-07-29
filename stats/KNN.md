1. cluster data with PCA
	1. this is training data
2. add new unclassified datapoint
3. classify datapoint using k nearest neighbours
	1. more k = categories with few samples easily outvoted, but less noisy
	2. less k = more noisy + die to outliers
https://neo4j.com/docs/graph-data-science/current/algorithms/knn/