==========
UNLOOPED C
==========

Introduction
============

Unlooped C is a C language extension for elegant implementation of algorithms using multi-dimensional arrays (eg. machine learning).

Unlooped C separates loop statements from formulas making C code as elegant as NumPy or Fortran 90.

Unlooped C is implemented in Python as C language preprocessor.

Examples
========

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
		
		free(best_k); free(k_cnt); del_rank(rnk);
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
