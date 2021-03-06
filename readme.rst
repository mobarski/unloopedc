==========
UNLOOPED C
==========

Introduction
============

Unlooped C is a C language extension for elegant implementation of algorithms using multi-dimensional arrays (eg. machine learning).

Unlooped C separates loop statements from formulas making C code as elegant as NumPy or Fortran 90.

Unlooped C is implemented in Python as C language preprocessor.

Code Examples
=============

K-means clustering (Manhattan distance):

.. code::

	void kmeans(float *data, float *center, int R, int C, int K, int I) {
		@def data[R,C];
		@def center[K,C];
		@new int best_k[R];
		@new int k_cnt[R];
		int r,c,k,i;
		float dist;
		rank rnk = new_rank(1);
		
		for (i=0; i<I; i++) {
			rank_reset(rnk);			// r@
			dist = 0.0; 				// r@ k@
			dist += fabs(center[k,c] - data[r,c]); 	// r@ k@ c@
			rank_push_min(rnk,dist,k);		// r@ k@
			k = rank_pop(rnk).i;			// r@
			best_k[r] = k;				// r@
			k_cnt[k] += 1;				// r@
			
			center[k,c] = 0.0;		// k@ c@
			k = best_k[r];			// r@
			center[k,c] += data[r,c];	// r@ c@
			center[k,c] /= k_cnt[k];	// k@ c@
		}
		
		free(k_cnt); free(best_k); del_rank(rnk);
	}

K-nearest neighbors (Manhattan distance):

.. code::

	int* knn(float *data, float *model, int R, int C, int M, int K) {
		@def data[R,C];
		@def model[M,C];
		@new int nn[R,K];
		int r,c,m,k;
		float dist;
		rank rnk = new_rank(K);
		
		rank_reset(rnk);			// r@
		dist = 0.0;				// r@ m@
		dist += fabs(model[m,c] - data[r,c]);	// r@ m@ c@
		rank_push_min(rnk,dist,m);		// r@ m@
		nn[r,k] = rank_pop(rnk).i;		// r@ k@
		
		del_rank(rnk);
		return nn;
	}	


Euclidean distance:

.. code::

	...
	dist = 0.0;			// r@ m@
	d = model[m,c] - data[r,c];	// r@ m@ c@
	dist += d*d;			// r@ m@ c@
	dist = sqrt(dist);		// r@ m@
	...


Minkowski distance:

.. code::

	...
	dist = 0.0;				// r@ m@
	dist += pow(model[m,c] - data[r,c], p);	// r@ m@ c@
	dist = pow(dist, 1/p);			// r@ m@	
	...


Weighted Manhattan distance:

.. code::

	...
	dist += weight[c] * fabs(model[m,c] - data[r,c]); // r@ m@ c@
	...

Chebyshev distance:

.. code::

	rank d_max = new_rank(1);
	...
	rank_reset(d_max);			// r@ m@
	d = fabs(model[m,c] - data[r,c]);	// r@ m@ c@
	rank_push_max(d_max,d,c);		// r@ m@ c@
	dist = rank_pop(d_max).f;		// r@ m@
	...

Loop Examples
=============

.. code::

	data[r] = 0; // r@       -- automatic loop boundaries
	data[r] = 0; // r@1:     -- explicit lower boundary, automatic upper
	data[r] = 0; // r@:10    -- explicit upper boundary, automatic lower
	
	data[r] = 0; // r@1:10   -- explicit boundaries
	data[r] = 0; // r@10     -- explicit loop count, 0 as implicit lower boundary, 10-1 as upper
	
	data[r,c] += sample[k,c]; // r@ k@  c@    -- multiple automatic loops
	data[r,c] += sample[k,c]; // r@ k@r c@:k  -- multiple loops with different boundries
	
	d = 0;          // r@       -- no array, loop boundries copied from closest loop
	d += data[r,c]; // r@1: c@  -- loop baundaries from this line will be used above and below
	out[r] = d;	// r@@      -- override automatic loop boundries, copy from closest loop
	
	data[r@,c] += sample[k@,c@4:10]; // loop definitions in the code

Array Definition Examples
=========================

.. code::
	
	@new float data[10,10,10];  // allocate new 3d float array
	@new int data[20,-10:20];   // allocate new 2d int array, with custom index range
	@def data[100,20,20];       // define dimensionality of existing pointer


Benchmark - kmeans
==================
	
	Rows: 1 million, Columns: 10, Iterations: 20, Clusters: 10
	
	====== ====================== ========================= ===
	time   software               hardware                  url
	====== ====================== ========================= ===
	14.3s  Unlooped C, gcc-4.9    Intel Atom N570
	44.5s  Hadoop 0.20, Hama 0.4  16 nodes, 256 cores        1 
	====== ====================== ========================= ===
	
	
	Rows: 0.1 million, Columns: 2, Iterations: 15, Clusters: 10

	====== ==================== =========================== ===
	time   software             hardware                    url
	====== ==================== =========================== ===
	0.43s  Unlooped C, gcc-4.9  Intel Atom N570            
	0.22s  C, gcc-4.8           Intel i7-2620M               2
	0.004s C, cuda 7.5          Nvidia GeForce GTX TITAN X   2
	====== ==================== =========================== ===

(1) http://wiki.apache.org/hama/Benchmarks
(2) https://github.com/andreaferretti/kmeans/blob/master/results

.. http://codingwiththomas.blogspot.com/2012/01/bsp-k-means-clustering-benchmark.html

OpenMP integration
==================

TODO

