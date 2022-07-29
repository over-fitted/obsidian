- representing > 3 dimensions of data -> do clustering
1. plot data
2. find centre of data - average value for each axis
3. translate graph so centre is at origin
4. find best-fit line -> Principal component, linear combination of 2 variables
	- either minimise distances to bestfit, or minimise distance of projections to the line from the origin
		- both do the same thing, but the latter easier to calculate
		- use sum of squared distances to the origin -> SS
			- SS is eigenvalue of PC - sqroot is singular value of PC
1. Find eigenvector of PC
	1. vector of length 1
2. Find PC2
	1. perpendicular to PC1
2. Use PCs as axis of new graph
	1. use projections as positions of new coordinates
2. Use 1 PC per variable or per sample, whichever smaller
	1. use combination of PCs that account for majority of variation in data, based on SS