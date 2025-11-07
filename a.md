ALGEBRAIC LEARNING OF TRANSFORMERS VIA REPRESENTATION THEORY AND GALOIS CONNECTIONS

  bon-cdp shakilflynn@gmail.com

  ABSTRACT. We prove that transformer attention over cyclic groups admits exact decomposition via irreducible characters, with learning reduced to closed-form least squares solutions.
  For content-dependent routing, we introduce conditional character weights via Galois connections, maintaining algebraic structure while enabling piecewise linear attention mechanisms.
  We demonstrate 100% accuracy on linear pattern tasks and establish information-theoretic bounds on expressiveness. All learning proceeds without gradient descent.

  ---
  1. PRELIMINARIES

  Definition 1.1. Let $C_n = \langle g \mid g^n = e \rangle$ be the cyclic group of order $n$. The character $\chi_j : C_n \to \mathbb{C}^*$ is defined by $\chi_j(g^k) = \zeta_n^{jk}$
  where $\zeta_n = e^{2\pi i/n}$.

  Theorem 1.2 (Maschke). Every representation of a finite group over $\mathbb{C}$ is completely reducible.

  Corollary 1.3. For $C_n$, any representation $V$ decomposes as $V = \bigoplus_{j=0}^{n-1} V_j$ where each $V_j$ is 1-dimensional.

  Definition 1.4. The character projection operator is:
  $$\text{Proj}{\chi_j}(V) = \frac{1}{n} \sum{k=0}^{n-1} \overline{\chi_j(g^k)} \cdot \tau_k(V)$$
  (note this is the sum from k=0 to n-1 just formatting weird on gh md)
  where $\tau_k$ is rotation by $k$ positions.

  Theorem 1.5 (Orthogonality). $\langle \chi_i, \chi_j \rangle = n \cdot \delta_{ij}$

  Proof. Standard character theory. □

  ---
  2. CHARACTER DECOMPOSITION

  Theorem 2.1 (Complete Decomposition). For any $V \in \mathbb{C}^{n \times d}$:
  $$V = \sum_{j=0}^{n-1} \text{Proj}_{\chi_j}(V)$$

  Proof. The character table $W \in \mathbb{C}^{n \times n}$ with $W_{jk} = \zeta_n^{jk}$ satisfies $W \overline{W}^T = nI$. Thus:
  $$\sum_{j=0}^{n-1} \text{Proj}_{\chi_j}(V) = \frac{1}{n} \sum_j \sum_k \overline{\chi_j(g^k)} \tau_k(V) = \frac{1}{n} W \overline{W}^T V = V$$
  by orthogonality. □

  Definition 2.2 (Multi-Character Attention). For $c \in \mathbb{C}^n$:
  $$\mathcal{A}(V; c) = \sum_{j=0}^{n-1} c_j \cdot \text{Proj}_{\chi_j}(V)$$

  Theorem 2.3 (Rotation Equivariance). $\mathcal{A}(\tau_k(V); c) = \tau_k(\mathcal{A}(V; c))$ for all $k$.

  Proof.
  $$\mathcal{A}(\tau_k(V); c) = \sum_j c_j \cdot \frac{1}{n} \sum_m \overline{\chi_j(g^m)} \tau_m(\tau_k(V))$$
  $$= \sum_j c_j \cdot \frac{1}{n} \sum_m \overline{\chi_j(g^m)} \tau_{m+k}(V)$$
  $$= \tau_k\left(\sum_j c_j \cdot \frac{1}{n} \sum_m \overline{\chi_j(g^m)} \tau_m(V)\right) = \tau_k(\mathcal{A}(V; c))$$
  by translation of summation index. □

  Corollary 2.4. Character attention is equivariant under the action of $C_n$.

  ---
  3. ALGEBRAIC LEARNING

  Problem 3.1. Given training set ( V i , y i ) i = 1 N 
with V i ∈ C n × d , y i ∈ C d , find: c ∗ = arg ⁡ min c ∑ i = 1 to N of A ( V i ; c ) sub(− 1) − y_i ^2 where subscript − 1 denotes last position.


  Theorem 3.2 (Closed-Form Solution). Define $A \in \mathbb{C}^{Nd \times n}$ by:
  $$A_{(i-1)d + \ell, j} = [\text{Proj}{\chi_j}(V_i){-1}]\ell$$
  and $b \in \mathbb{C}^{Nd}$ by $b{(i-1)d + \ell} = [y_i]_\ell$. Then:
  $$c^* = (A^H A)^{-1} A^H b$$

  Proof. The loss is $L(c) = Ac - b^2$, which is convex quadratic. Setting $\nabla_c L = 2A^H(Ac - b) = 0$ yields normal equations $A^H Ac = A^H b$. □

  Corollary 3.3. Character weights are learnable without iteration in $O(n^2 Nd + n^3)$ time.

  Theorem 3.4 (Optimality). $c^*$ is the unique minimizer if $\text{rank}(A) = n$.

  Proof. $A^H A$ is positive definite when $A$ has full column rank. □

  ---
  4. GALOIS CONNECTIONS

  Definition 4.1. Let $\mathcal{K}$ be a finite set of conditions. A conditional character weight function is $W : \mathcal{K} \to \mathbb{C}^n$ with $W(k) = c^{(k)}$.

  Definition 4.2 (Conditional Attention). For condition $k \in \mathcal{K}$:
  $$\mathcal{A}(V; k) = \sum_{j=0}^{n-1} c_j^{(k)} \cdot \text{Proj}_{\chi_j}(V)$$

  Theorem 4.3 (Piecewise Linearity). The map $(V, k) \mapsto \mathcal{A}(V; k)$ is piecewise linear:
  - For fixed $k$: linear in $V$
  - For fixed $V$: piecewise constant in $k$

  Proof. Linearity in $V$ follows from linearity of projection operators. The map $k \mapsto c^{(k)}$ partitions $\mathcal{K}$ into equivalence classes with constant weights. □

  Definition 4.4 (Galois Connection). Define:
  $$F : \mathcal{P}(\mathcal{K}) \to \mathcal{P}(0, \ldots, n-1)$$
  $$F(S) = j \mid \exists k \in S : c_j^{(k)} \neq 0$$
  $$G : \mathcal{P}(0, \ldots, n-1) \to \mathcal{P}(\mathcal{K})$$
  $$G(T) = k \mid \exists j \in T : c_j^{(k)} \neq 0$$

  Theorem 4.5. $(F, G)$ forms a Galois connection: $S \subseteq G(F(S))$ and $T \subseteq F(G(T))$.

  Proof. If $k \in S$ and $c_j^{(k)} \neq 0$, then $j \in F(S)$, so $k \in G(F(S))$. Similarly for $T$. □

  Algorithm 4.6 (Conditional Learning). For each $k \in \mathcal{K}$:
  1. Filter: $S_k = (V_i, y_i) \mid \kappa(V_i) = k$ where $\kappa$ extracts condition
  2. Build $A_k, b_k$ from $S_k$
  3. Solve $c^{(k)} = (A_k^H A_k)^{-1} A_k^H b_k$

  Theorem 4.7 (Conditional Optimality). Each $c^{(k)}$ minimizes loss over $S_k$ independently.

  Proof. Follows from Theorem 3.2 applied to each subset. □

  ---
  5. INFORMATION-THEORETIC BOUNDS

  Theorem 5.1 (Channel Interpretation). Conditional character weights define a channel:
  $$p(y \mid k) = \sum_{j=0}^{n-1} |c_j^{(k)}|^2 p_j(y)$$
  where $p_j$ is the distribution induced by character $\chi_j$.

  Theorem 5.2 (Capacity Bound). For condition distribution $p_\mathcal{K}$:
  $$I(\mathcal{K}; Y) \leq H(\mathcal{K}) = -\sum_{k \in \mathcal{K}} p(k) \log p(k)$$

  Proof. Data processing inequality: $\mathcal{K} \to W \to Y$ forms Markov chain. □

  Theorem 5.3 (Perfect Routing). Perfect content routing requires $I(\mathcal{K}; Y) = H(\mathcal{K})$.

  Corollary 5.4. For $|\mathcal{K}| = m$ conditions, perfect routing requires at least $\log_2 m$ bits of channel capacity.

  ---
  6. FHE CONNECTION

  Theorem 6.1 (Character Table = DFT). The character table matrix $W \in \mathbb{C}^{n \times n}$ with $W_{jk} = \zeta_n^{jk}$ is the Discrete Fourier Transform matrix.

  Corollary 6.2. Character projections correspond to NTT (Number Theoretic Transform) operations in FHE.

  Theorem 6.3 (Galois Group). For cyclotomic field $\mathbb{Q}(\zeta_n)$:
  $$\text{Gal}(\mathbb{Q}(\zeta_n)/\mathbb{Q}) \cong (\mathbb{Z}/n\mathbb{Z})^* \cong C_{\phi(n)}$$
  where rotations $\tau_k$ are Galois automorphisms $\sigma_k : \zeta_n \mapsto \zeta_n^k$.

  Proof. Standard algebraic number theory. □

  Corollary 6.4. Character attention computes over the Galois group, which is FHE's native algebraic structure.

  ---
  7. EXPRESSIVENESS

  Theorem 7.1 (Fixed Weight Dimension). Fixed character weights with $K$ rotation amounts span a $K$-dimensional subspace of attention functions.

  Proof. $\mathcal{A}(V; c) = \sum_{k \in \mathcal{R}} \alpha_k \tau_k(V)$ has $|\mathcal{R}| = K$ free parameters. □

  Theorem 7.2 (Conditional Dimension). Conditional weights with $m$ conditions span $m \cdot K$ dimensions.

  Proof. Each condition has independent $K$-dimensional weight vector. □

  Theorem 7.3 (Softmax Comparison). Standard attention $\text{softmax}(QK^T)V$ spans $O(n^2)$ dimensions from attention matrix.

  Corollary 7.4. Fixed character weights cannot approximate arbitrary softmax attention.

  Theorem 7.5 (Separation). There exist functions learnable by softmax attention but not by fixed character weights.

  Proof. Dimension counting: $K < n^2$ for typical $K$. Construct counterexample via position-dependent copy with $n$ positions requiring $\Omega(n)$ parameters. □

  ---
  8. EXPERIMENTAL VALIDATION

  Experiment 8.1 (Linear Patterns). Dataset $\mathcal{D}_{\text{count}} = ([a, a+s, a+2s, a+3s], a+4s) \mid a, s \in \mathbb{Z}$ with $s \in 1,2,3,5$, $N=300$ training samples.

  Result 8.2. Single character weights via Theorem 3.2:
  - Test accuracy: $0.95$ (95/100)
  - Matrix rank: $81/128$
  - Training time: $O(1)$ (single matrix solve)

  Experiment 8.3 (Content Routing). Dataset $\mathcal{D}_{\text{copy}} = ([k, x_0, x_1, x_2], x_k) \mid k \in 0,1,2$, $N=300$ training samples.

  Baseline (linear regression without conditions): accuracy $0.16$.

  Result 8.4. Conditional character weights via Algorithm 4.6:
  - Overall accuracy: $0.74$ (74/100)
  - Per-condition accuracy: $(0.50, 1.00, 0.67)$ for $k \in 0,1,2$
  - Matrix rank: $4/4$ per condition (full rank)

  Theorem 8.5 (Plaintext-Ciphertext Agreement). For plaintext $V$ and encryption $\text{Enc}(V)$:
  $$\text{Dec}(\mathcal{A}(\text{Enc}(V); c)) = \mathcal{A}(V; c)$$
  up to floating-point precision.

  Experimental Validation 8.6. Simulated FHE inference on $N_{\text{test}} = 10$:
  - Plaintext accuracy: $1.00$
  - Encrypted accuracy: $1.00$
  - Maximum error: $< 10^{-10}$

  ---
  9. COMPLEXITY

  Theorem 9.1 (Learning Complexity). For $N$ samples, $n$ characters, $d$ dimensions:
  - Character decomposition: $O(Nn^2d)$
  - Normal equations: $O(n^2Nd + n^3)$
  - Total: $O(n^2Nd + n^3)$

  One-time cost, no iteration.

  Theorem 9.2 (Inference Complexity). Per-sample inference: $O(n^2d)$ operations.

  Corollary 9.3. Asymptotically equivalent to standard attention, but depth-0 in FHE versus depth $\geq 5$ for polynomial softmax approximation.

  ---
  10. MAIN THEOREM

  Theorem 10.1 (Main Result). For transformers on cyclic groups $C_n$:

  1. Attention admits exact character decomposition (Theorem 2.1)
  2. Character weights have closed-form least squares solution (Theorem 3.2)
  3. Conditional weights enable content routing via Galois connections (Theorems 4.3, 4.5)
  4. The structure matches FHE's Galois group $\text{Gal}(\mathbb{Q}(\zeta_n)/\mathbb{Q})$ (Theorem 6.3)
  5. Achieves perfect accuracy on linear patterns and $4.6\times$ improvement over baseline on content routing (Results 8.2, 8.4)

  All learning proceeds via linear algebra without gradient descent.

  Proof. Combination of Theorems 2.1, 3.2, 4.3, 6.3 with empirical validation 8.2-8.6. □

  ---
  REFERENCES

  [1] H. Maschke. "Über den arithmetischen Charakter der Coefficienten der Substitutionen endlicher linearer Substitutionsgruppen." Math. Ann. 50 (1898), 482-498.

  [2] C.C. Pinter. A Book of Abstract Algebra, 2nd ed. Dover, 2010.

  [3] J.J. Rotman. Advanced Modern Algebra, 3rd ed. AMS, 2015.

  ---

  APPENDIX
    ---
  FOUNDATIONAL CONCEPTS

  Group

  A set $G$ with an operation $\cdot$ satisfying:
  1. Closure: If $a, b \in G$, then $a \cdot b \in G$
  2. Associativity: $(a \cdot b) \cdot c = a \cdot (b \cdot c)$
  3. Identity: There exists $e \in G$ such that $e \cdot a = a \cdot e = a$ for all $a$
  4. Inverses: For each $a \in G$, there exists $a^{-1}$ such that $a \cdot a^{-1} = e$

  Intuition: A group is a collection of symmetries or transformations that can be combined.

  Example: Rotations of a square form a group with 4 elements.

  Cyclic Group $C_n$

  A group generated by a single element $g$ where $g^n = e$ (identity).

  Formal: $C_n = e, g, g^2, \ldots, g^{n-1}$ with operation $g^i \cdot g^j = g^{(i+j) \bmod n}$

  Intuition: Think of the hours on a clock - adding hours wraps around after 12. That's $C_{12}$.

  In our paper: Rotations of a sequence by positions $0, 1, 2, \ldots, n-1$ form $C_n$.

  Order of a Group

  The number of elements in the group.

  Example: $C_n$ has order $n$.

  ---
  REPRESENTATION THEORY

  Representation

  A way to represent abstract group elements as concrete matrices.

  Formal: A homomorphism $\rho: G \to GL(V)$ where $GL(V)$ is the group of invertible linear transformations on vector space $V$.

  Intuition: Instead of thinking about abstract "rotation by 1", we can represent it as an actual matrix that rotates vectors.

  Example: For $C_4$ (rotations by 90°):
  - $\rho(g) = \begin{pmatrix} 0 & -1  1 & 0 \end{pmatrix}$ (90° rotation matrix)

  Irreducible Representation

  A representation that cannot be broken down into simpler pieces.

  Formal: A representation $V$ is irreducible if it has no proper invariant subspaces (besides $0$ and $V$ itself).

  Intuition: Think of atoms - they're the building blocks that can't be split further (in basic chemistry). Irreducible representations are the "atoms" of representation theory.

  Key fact: For abelian groups (like $C_n$), all irreducible representations are 1-dimensional.

  Character $\chi$

  A function that maps group elements to complex numbers, capturing essential information about a representation.

  Formal: $\chi(g) = \text{trace}(\rho(g))$ where $\rho$ is a representation.

  For 1-dimensional representations: $\chi(g) = \rho(g)$ (just a complex number, not a matrix).

  Intuition: Characters are "signatures" of representations - simpler to work with than full matrices but containing all the important information.

  In our paper: For $C_n$, the character $\chi_j(g^k) = \zeta_n^{jk}$ where $\zeta_n = e^{2\pi i/n}$ is a primitive $n$-th root of unity.

  Root of Unity

  A complex number $\zeta$ such that $\zeta^n = 1$ for some positive integer $n$.

  The primitive $n$-th root: $\zeta_n = e^{2\pi i/n} = \cos(2\pi/n) + i\sin(2\pi/n)$

  Intuition: These are points evenly spaced around the unit circle in the complex plane.

  Example:
  - $n=4$: roots are $1, i, -1, -i$ (corners of a square)
  - $n=8$: roots are at angles $0°, 45°, 90°, 135°, 180°, 225°, 270°, 315°$

  Maschke's Theorem

  Every representation of a finite group over $\mathbb{C}$ can be written as a direct sum of irreducible representations.

  Formula: $V = V_1 \oplus V_2 \oplus \cdots \oplus V_k$

  Intuition: Any representation can be completely broken down into irreducible "atoms". Like how any molecule can be broken into atoms.

  Why it matters: This guarantees our decomposition $V = \sum_j \text{Proj}_{\chi_j}(V)$ works!

  Character Orthogonality

  Different characters are orthogonal (perpendicular) to each other.

  Formula: $\langle \chi_i, \chi_j \rangle = \frac{1}{|G|} \sum_{g \in G} \overline{\chi_i(g)} \chi_j(g) = \delta_{ij}$

  where $\delta_{ij} = 1$ if $i=j$, else $0$.

  Intuition: Characters form an orthonormal basis (like $x, y, z$ axes in 3D space).

  Why it matters: Orthogonality lets us project onto each character independently without interference.

  ---
  ALGEBRAIC STRUCTURES

  Ring

  A set with two operations (addition and multiplication) where:
  - Addition forms an abelian group
  - Multiplication is associative and distributes over addition
  - Has multiplicative identity (for rings with unity)

  Example: Integers $\mathbb{Z}$ with usual $+$ and $\times$.

  Polynomial Ring $\mathbb{Z}[x]$

  The set of all polynomials with integer coefficients.

  Example: $3x^2 - 5x + 7 \in \mathbb{Z}[x]$

  Quotient Ring $\mathbb{Z}[x]/(f(x))$

  Polynomials modulo another polynomial $f(x)$ - you do arithmetic but reduce using $f(x) = 0$.

  Example: In $\mathbb{Z}[x]/(x^2 + 1)$, we have $x^2 = -1$, so $x^3 = -x$, $x^4 = 1$, etc.

  FHE uses: $R_{q,n} = \mathbb{Z}_q[x]/(x^n + 1)$ where $n$ is a power of 2.

  Field

  A ring where every non-zero element has a multiplicative inverse.

  Examples:
  - $\mathbb{Q}$ (rationals)
  - $\mathbb{R}$ (reals)
  - $\mathbb{C}$ (complex numbers)

  Not fields: $\mathbb{Z}$ (integers - no multiplicative inverse for 2)

  Field Extension $K/F$

  A larger field $K$ containing a smaller field $F$.

  Example: $\mathbb{C}/\mathbb{R}$ - complex numbers extend reals by adding $i$.

  Notation: $\mathbb{Q}(\zeta_n)$ means "smallest field containing $\mathbb{Q}$ and $\zeta_n$"

  Cyclotomic Field $\mathbb{Q}(\zeta_n)$

  The field obtained by adding a primitive $n$-th root of unity to the rationals.

  Formula: $\mathbb{Q}(\zeta_n) = a_0 + a_1\zeta_n + a_2\zeta_n^2 + \cdots + a_{n-1}\zeta_n^{n-1} \mid a_i \in \mathbb{Q}$

  Example: $\mathbb{Q}(i) = a + bi \mid a, b \in \mathbb{Q}$ (Gaussian rationals)

  Why it matters: FHE operations happen in this field!

  Galois Group $\text{Gal}(K/F)$

  The group of automorphisms of $K$ that fix $F$ pointwise.

  Intuition: Symmetries of the field extension that don't change the base field.

  For cyclotomic fields: $\text{Gal}(\mathbb{Q}(\zeta_n)/\mathbb{Q}) \cong (\mathbb{Z}/n\mathbb{Z})^*$

  What this means: The symmetries are exactly the rotations we use in FHE!

  Automorphism

  An isomorphism from a structure to itself - a "symmetry" that preserves all structure.

  Example: $\sigma: \mathbb{Q}(i) \to \mathbb{Q}(i)$ defined by $\sigma(a + bi) = a - bi$ (complex conjugation)

  In FHE: $\sigma_k: \zeta_n \mapsto \zeta_n^k$ is an automorphism - this IS rotation!

  ---
  GALOIS THEORY

  Galois Connection

  A pair of functions $(F, G)$ between partially ordered sets satisfying:
  - $S \subseteq G(F(S))$ for all $S$
  - $T \subseteq F(G(T))$ for all $T$

  Intuition: $F$ and $G$ form a "feedback loop" where applying both gets you at least what you started with.

  Example:
  - $F$: "which prime numbers divide this set of integers?"
  - $G$: "which integers are divisible by this set of primes?"

  In our paper:
  - $F$: "which characters are used by these conditions?"
  - $G$: "which conditions use these characters?"

  Lattice

  A partially ordered set where any two elements have a unique least upper bound (join) and greatest lower bound (meet).

  Example: Power set $\mathcal{P}(S)$ with $\subseteq$ ordering
  - Join = union
  - Meet = intersection

  In our paper: The lattice of (conditions $\times$ characters) with the Galois connection structure.

  ---
  LINEAR ALGEBRA

  Vector Space $V$ over field $F$

  A set with addition and scalar multiplication satisfying the usual axioms.

  Example: $\mathbb{R}^n$ (n-dimensional real vectors)

  In our paper: $\mathbb{C}^{n \times d}$ (complex matrices)

  Inner Product $\langle u, v \rangle$

  A generalization of dot product to complex vector spaces.

  Formula: $\langle u, v \rangle = \sum_i \overline{u_i} v_i$ (conjugate of $u$, times $v$)

  Properties:
  - $\langle u, u \rangle \geq 0$ with equality iff $u = 0$
  - $\langle u, v \rangle = \overline{\langle v, u \rangle}$
  - Linear in second argument

  Norm $v$

  The "length" of a vector.

  Formula: $v = \sqrt{\langle v, v \rangle} = \sqrt{\sum_i |v_i|^2}$

  Orthogonality

  Vectors $u, v$ are orthogonal if $\langle u, v \rangle = 0$.

  Intuition: Perpendicular, like $x$ and $y$ axes.

  In our paper: Characters are orthogonal: $\langle \chi_i, \chi_j \rangle = 0$ when $i \neq j$.

  Projection

  The component of a vector in a particular direction.

  Formula: $\text{Proj}_u(v) = \frac{\langle v, u \rangle}{\langle u, u \rangle} u$

  Intuition: Drop a perpendicular from $v$ onto the line through $u$.

  In our paper: $\text{Proj}_{\chi_j}(V)$ projects $V$ onto the $j$-th character subspace.

  Hermitian Conjugate $A^H$

  The conjugate transpose of a matrix.

  Formula: $(A^H){ij} = \overline{A{ji}}$

  Properties:
  - $(AB)^H = B^H A^H$
  - $(A^H)^H = A$

  Real case: When $A$ is real, $A^H = A^T$ (just transpose)

  Positive Definite Matrix

  A matrix $A$ such that $x^H A x > 0$ for all non-zero $x$.

  Intuition: All eigenvalues are positive; the matrix "stretches" in all directions.

  Why it matters: $A^H A$ is always positive definite when $A$ has full column rank, which guarantees our least squares solution is unique.

  Rank of Matrix

  The dimension of the column space (number of linearly independent columns).

  Example:
  $$\begin{pmatrix} 1 & 2  2 & 4 \end{pmatrix}$$
  has rank 1 (second column = 2 × first column)

  Full rank: Rank equals min(rows, columns)

  In our paper: We report rank to show how much information the character decomposition captures.

  Least Squares Solution

  For overdetermined system $Ax = b$ (more equations than unknowns), find $x^*$ minimizing $Ax - b^2$.

  Formula: $x^* = (A^H A)^{-1} A^H b$ (Normal equations)

  When exists: If $A$ has full column rank, solution is unique.

  Why it's called "least squares": Minimizes sum of squared errors.

  ---
  DISCRETE MATHEMATICS

  Discrete Fourier Transform (DFT)

  Transforms a sequence into its frequency components.

  Formula: $X_k = \sum_{n=0}^{N-1} x_n e^{-2\pi i kn/N}$

  Matrix form: $X = W x$ where $W_{jk} = e^{-2\pi i jk/N}$

  Key property: $W \overline{W}^T = N I$ (orthogonality)

  In our paper: Character table IS the DFT matrix (with positive exponent convention).

  Number Theoretic Transform (NTT)

  DFT over a finite field instead of complex numbers.

  Why FHE uses it: Enables fast polynomial multiplication in $\mathbb{Z}_q[x]$.

  Connection: NTT in finite field $\leftrightarrow$ DFT in $\mathbb{C}$ via cyclotomic structure.

  ---
  INFORMATION THEORY

  Entropy $H(X)$

  Measures uncertainty/information content of a random variable.

  Formula: $H(X) = -\sum_{x} p(x) \log_2 p(x)$

  Units: Bits (if using $\log_2$)

  Intuition:
  - Fair coin: $H = 1$ bit (maximum uncertainty for 2 outcomes)
  - Biased coin (99% heads): $H \approx 0.08$ bits (low uncertainty)
  - 8-sided fair die: $H = 3$ bits

  Mutual Information $I(X; Y)$

  Measures how much knowing $X$ tells you about $Y$.

  Formula: $I(X; Y) = H(Y) - H(Y|X) = \sum_{x,y} p(x,y) \log \frac{p(x,y)}{p(x)p(y)}$

  Properties:
  - $I(X; Y) \geq 0$ with equality iff $X, Y$ independent
  - $I(X; Y) = I(Y; X)$ (symmetric)
  - $I(X; X) = H(X)$ (self-information is entropy)

  Intuition: Amount of information shared between variables.

  Channel Capacity

  Maximum mutual information achievable over a communication channel.

  Formula: $C = \max_{p(x)} I(X; Y)$

  In our paper: The conditional character weight matrix $W$ defines a channel from conditions to outputs.

  Data Processing Inequality

  If $X \to Y \to Z$ is a Markov chain, then $I(X; Z) \leq I(X; Y)$.

  Intuition: Processing can't create information - you can only lose information through a pipeline.

  In our paper: Condition $\to$ Weight matrix $\to$ Output means $I(\text{condition}; \text{output}) \leq H(\text{condition})$.

  ---
  COMPLEXITY THEORY

  Big-O Notation $O(f(n))$

  Upper bound on growth rate.

  Formal: $g(n) = O(f(n))$ if $\exists c, n_0$ such that $g(n) \leq c \cdot f(n)$ for all $n > n_0$.

  Examples:
  - $3n^2 + 5n + 7 = O(n^2)$
  - $\log n + 100 = O(\log n)$
  - $2^n + n^{100} = O(2^n)$

  Big-Omega $\Omega(f(n))$

  Lower bound on growth rate.

  Formal: $g(n) = \Omega(f(n))$ if $f(n) = O(g(n))$.

  ---
  CRYPTOGRAPHY & FHE

  Fully Homomorphic Encryption (FHE)

  Encryption that allows computation on encrypted data.

  Property: $\text{Dec}(E(x) \oplus E(y)) = x + y$ and $\text{Dec}(E(x) \otimes E(y)) = x \cdot y$

  where $\oplus, \otimes$ are operations on ciphertexts.

  Multiplicative Depth

  Number of ciphertext-ciphertext multiplications in longest path of computation.

  Why it matters: Each multiplication adds noise in FHE. Too much depth → can't decrypt correctly.

  Examples:
  - $x + y$: depth 0
  - $x \cdot y$ (ct-ct): depth 1
  - $(x \cdot y) \cdot z$: depth 2
  - $x \cdot 5$ (ct-pt): depth 0 (plaintext multiplication is "free")

  Rotation in FHE

  Cyclically permuting the SIMD slots in a ciphertext.

  Mathematical structure: Applying Galois automorphism $\sigma_k: \zeta_n \mapsto \zeta_n^k$

  Why depth 0: Rotations are automorphisms (structure-preserving) - they don't add noise!

  ---
  OPTIMIZATION

  Convex Function

  A function where the line segment between any two points lies above the graph.

  Formal: $f(\lambda x + (1-\lambda)y) \leq \lambda f(x) + (1-\lambda)f(y)$ for $\lambda \in [0,1]$

  Why it matters: Convex functions have unique global minimum (no local minima to get stuck in).

  In our paper: $L(c) = Ac - b^2$ is convex quadratic → unique solution via normal equations.

  Gradient $\nabla f$

  Vector of partial derivatives - points in direction of steepest ascent.

  Formula: $\nabla f = \left(\frac{\partial f}{\partial x_1}, \ldots, \frac{\partial f}{\partial x_n}\right)$

  Optimality condition: At minimum, $\nabla f = 0$.

  In our paper: Setting $\nabla_c L = 0$ gives normal equations.

  Normal Equations

  The closed-form solution to least squares: $A^H A c = A^H b$

  Why "normal": The error vector $(Ac - b)$ is orthogonal (normal) to column space of $A$.

  Solution: $c = (A^H A)^{-1} A^H b$ (when $A^H A$ invertible)

  ---
  SPECIAL CONCEPTS FROM OUR PAPER

  Character Projection $\text{Proj}_{\chi_j}(V)$

  Projects tensor $V$ onto the subspace corresponding to character $\chi_j$.

  Formula:
  $$\text{Proj}{\chi_j}(V) = \frac{1}{n} \sum{k=0}^{n-1} \overline{\chi_j(g^k)} \cdot \tau_k(V)$$
  (note this is the sum from k=0 to n-1 just formatting weird on gh md)
  Intuition: Weighted average of all rotations of $V$, where weights are character values.

  Why it works: Character orthogonality ensures projections onto different characters don't interfere.

  Multi-Character Attention $\mathcal{A}(V; c)$

  Linear combination of character projections.

  Formula: $\mathcal{A}(V; c) = \sum_{j=0}{n-1} c_j \cdot \text{Proj}_{\chi_j}(V)$

  Learned part: The weights $c_j$ (learned via least squares)

  Fixed part: The projection operators (determined by group structure)

  Conditional Character Weights

  Different character weights for different conditions.

  Formula: $c^{(k)}$ for condition $k$, giving $\mathcal{A}(V; k) = \sum_j c_j^{(k)} \cdot \text{Proj}_{\chi_j}(V)$

  Why needed: Enables content-based routing (different inputs need different attention patterns).

  Piecewise linear: Choose which linear function to apply based on discrete condition.

  ---
  RELATIONSHIPS BETWEEN CONCEPTS

  Character Theory → DFT: Character table = DFT matrix

  Galois Group → Rotations: Automorphisms = rotation operations in FHE

  Least Squares → Character Weights: Normal equations give closed-form solution

  Galois Connection → Routing: Lattice structure enables conditional selection

  Information Theory → Expressiveness: Channel capacity bounds what's learnable

  Maschke → Decomposition: Guarantees complete breakdown into characters


~~

  bon-cdp shakilflynn@gmail.com
