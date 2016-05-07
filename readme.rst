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


Benchmark
=========

	====== ====================== ======== ===== ========
	task   config                 unlooped numpy gfortran
	====== ====================== ======== ===== ========
	kmeans R=1000 C=100 K=10 I=10 .        .     .
	====== ====================== ======== ===== ========

.. http://codingwiththomas.blogspot.com/2012/01/bsp-k-means-clustering-benchmark.html
