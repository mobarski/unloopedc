==========
UNLOOPED C
==========

Introduction
============

Unlooped C is a C language extension for elegant implementation of multi-dimensional array algorithms (eg. machine learning).

Unlooped C separates loop statements from formulas making C code as elegant as NumPy or Fortran 90.

.. code::

	void kmeans(float *data, double *center, int R, int C, int K, int I) {
		@def data[R,C];
		@def center[K,C];
		@new int best_k[R];
		@new int k_cnt[R];
		int r,c,k,i,kmin;
		float dist;
		
		for (i=0; i<I; i++) {
			dist = 0.0; 				// r@ k@
			dist += fabs(center[k,c] - data[r,c]); 	// r@ k@ c@
			kmin = @min_var(dist,k);		// r@ k@
			best_k[r] = kmin;			// r@
			k_cnt[kmin] += 1;			// r@
			
			center[k,c] = 0.0;		// k@ c@
			k = best_k[r];			// r@
			center[k,c] += data[r,c];	// r@ c@
			center[k,c] /= k_cnt[k];	// k@ c@
		}
	}
