## Chapter 20. K-means Clustering

Lioyd Max Quantization 

Quantization is a kind of clustering
* Data coding and compression - Quantization : codewords
* Pattern recognition - Clustering : Representative patterns 

동작 과정
1. Select K initial centroid vector(좌표)
	* Random or Simple rules
2. Find the closest vectors for every centroid using Euclidean distances 
	* Construct a tentative cluster for every controid
3. Update the centroid(=mean) of every cluster
4. Repeat 2 and 3 processes until stop conditions 
	* little change of centroids
	* Maximum iteration number

