# Quantum Gates from First Principles

A derivation of how quantum gates work — starting from the postulates of quantum
mechanics, and building up to the Hadamard gate, the Pauli gates, rotations, and
two-qubit gates. The goal is not to memorize matrices but to see *where each
matrix comes from*.

---

## 1. The qubit: a state is a vector

A classical bit is either `0` or `1`. A **qubit** is a unit vector in a
two-dimensional complex vector space, $\mathbb{C}^2$ (a Hilbert space).

Pick two orthonormal basis vectors and call them the *computational basis*:

$$
|0\rangle = \begin{pmatrix} 1 \\ 0 \end{pmatrix}, \qquad
|1\rangle = \begin{pmatrix} 0 \\ 1 \end{pmatrix}.
$$

A general qubit state is a linear combination — a **superposition**:

$$
|\psi\rangle = \alpha\,|0\rangle + \beta\,|1\rangle
            = \begin{pmatrix}\alpha \\ \beta\end{pmatrix},
\qquad \alpha,\beta \in \mathbb{C}.
$$

The numbers $\alpha, \beta$ are *amplitudes*. They are not probabilities — they
are complex, and they can interfere (add and cancel), which is the entire source
of quantum computing's power.

### The Born rule and normalization

**Postulate (measurement).** If you measure $|\psi\rangle$ in the computational
basis, you get outcome `0` with probability $|\alpha|^2$ and outcome `1` with
probability $|\beta|^2$, and the state collapses to the measured basis vector.

Because total probability must be 1:

$$
|\alpha|^2 + |\beta|^2 = 1.
$$

This is why states are **unit vectors**: $\langle\psi|\psi\rangle = 1$.

### Global vs. relative phase

Multiplying the whole state by a phase $e^{i\gamma}$ changes nothing measurable
($|e^{i\gamma}\alpha|^2 = |\alpha|^2$), so **global phase is physically
irrelevant**. But the *relative* phase between $\alpha$ and $\beta$ is real and
observable — it is what distinguishes $|+\rangle$ from $|-\rangle$ below. Keep
this distinction; it explains why many gate identities hold only "up to global
phase."

### The Bloch sphere (geometric picture)

Up to global phase, any qubit can be written

$$
|\psi\rangle = \cos\tfrac{\theta}{2}\,|0\rangle
             + e^{i\varphi}\sin\tfrac{\theta}{2}\,|1\rangle,
$$

which maps to a point $(\theta,\varphi)$ on the surface of a unit sphere. `|0⟩`
is the north pole, `|1⟩` the south pole. **Every single-qubit gate is a rotation
of this sphere** — this is the picture to hold onto.

---

## 2. Why gates are unitary matrices

We haven't assumed gates are matrices — we can *derive* it.

**Postulate (evolution).** A closed quantum system evolves according to the
Schrödinger equation, where $\mathcal{H}$ is the **Hamiltonian** (a Hermitian
operator, the energy of the system) and $\hbar$ is the reduced Planck constant:

$$
i\hbar\,\frac{d}{dt}|\psi(t)\rangle = \mathcal{H}\,|\psi(t)\rangle.
$$

For a time-independent $\mathcal{H}$ this integrates to

$$
|\psi(t)\rangle = U(t)\,|\psi(0)\rangle,
\qquad U(t) = e^{-i\mathcal{H}t/\hbar}.
$$

**Claim: $U$ is unitary**, i.e. $U^\dagger U = I$. Because $\mathcal{H}$ is
Hermitian ($\mathcal{H}^\dagger = \mathcal{H}$):

$$
U^\dagger = \left(e^{-i\mathcal{H}t/\hbar}\right)^\dagger
          = e^{+i\mathcal{H}^\dagger t/\hbar}
          = e^{+i\mathcal{H}t/\hbar}
          = U^{-1}.
$$

So $U^\dagger U = I$. This is the whole reason gates are unitary:

- **Unitary ⇒ preserves length**: $\langle\psi|U^\dagger U|\psi\rangle =
  \langle\psi|\psi\rangle$, so a normalized state stays normalized — probability
  is conserved.
- **Unitary ⇒ reversible**: $U^{-1} = U^\dagger$ always exists, so every quantum
  gate can be undone. (This is why there is no quantum "AND" gate as such —
  AND throws information away and isn't invertible.)

**A quantum gate is exactly a unitary matrix**, and physically it is realized by
turning on a chosen Hamiltonian for a chosen amount of time.

---

## 3. The Hadamard gate

The star of the show. Its matrix is

$$
H = \frac{1}{\sqrt{2}}
\begin{pmatrix} 1 & 1 \\ 1 & -1 \end{pmatrix}.
$$

### 3.1 What it does

$$
H|0\rangle = \frac{1}{\sqrt2}\begin{pmatrix}1\\1\end{pmatrix}
           = \frac{|0\rangle + |1\rangle}{\sqrt2} \equiv |+\rangle,
\qquad
H|1\rangle = \frac{1}{\sqrt2}\begin{pmatrix}1\\-1\end{pmatrix}
           = \frac{|0\rangle - |1\rangle}{\sqrt2} \equiv |-\rangle.
$$

It takes a definite bit and produces an **equal superposition** — a coin that is
genuinely 50/50 when measured ($|\pm\tfrac{1}{\sqrt2}|^2 = \tfrac12$). This is
the standard way a quantum algorithm "spreads" a register across all inputs at
once. The minus sign in $|-\rangle$ is the relative phase that lets later
interference tell $|+\rangle$ and $|-\rangle$ apart.

### 3.2 It is its own inverse

$$
H^2 = \frac{1}{2}
\begin{pmatrix}1&1\\1&-1\end{pmatrix}
\begin{pmatrix}1&1\\1&-1\end{pmatrix}
= \frac{1}{2}\begin{pmatrix}2&0\\0&2\end{pmatrix} = I.
$$

So $H = H^\dagger = H^{-1}$: applying it twice returns you to the start (it takes
$|+\rangle$ back to $|0\rangle$). It is both **Hermitian** and **unitary**.

### 3.3 Where does this matrix come from? (four derivations)

The Hadamard matrix isn't arbitrary. Here are four independent ways to *derive*
it — each illuminates a different aspect.

#### Derivation A — from requirements

Ask for the simplest gate that (i) is real, (ii) is unitary, (iii) sends
$|0\rangle$ to the equal superposition $(|0\rangle+|1\rangle)/\sqrt2$, and
(iv) is its own inverse.

Every real $2\times2$ unitary (orthogonal) matrix is either a rotation or a
reflection:

$$
R(\theta) = \begin{pmatrix}\cos\theta & -\sin\theta\\ \sin\theta & \cos\theta\end{pmatrix}
\ (\det = +1),
\qquad
M(\theta) = \begin{pmatrix}\cos\theta & \sin\theta\\ \sin\theta & -\cos\theta\end{pmatrix}
\ (\det = -1).
$$

Requirement (iii) forces the first column to be $(1/\sqrt2,\,1/\sqrt2)$, i.e.
$\theta = 45^\circ$. The rotation choice $R(45^\circ)$ fails requirement (iv):
$R(45^\circ)^2 = R(90^\circ) \neq I$. But a **reflection always squares to the
identity**, $M(\theta)^2 = I$, so it automatically satisfies (iv). Taking
$\theta = 45^\circ$ in the reflection:

$$
M(45^\circ) = \begin{pmatrix}\tfrac1{\sqrt2} & \tfrac1{\sqrt2}\\[2pt]
\tfrac1{\sqrt2} & -\tfrac1{\sqrt2}\end{pmatrix} = H.
$$

So **the Hadamard gate is the reflection about the $22.5^\circ$ axis** in the
plane — the unique real, self-inverse gate that creates equal superpositions.

#### Derivation B — it is the Fourier transform on one qubit

The discrete Fourier transform on $N$ points is the matrix with entries
$\frac{1}{\sqrt N}\,\omega^{jk}$ where $\omega = e^{2\pi i/N}$. For a single
qubit $N = 2$, so $\omega = e^{i\pi} = -1$:

$$
F_2 = \frac{1}{\sqrt2}
\begin{pmatrix} \omega^{0} & \omega^{0} \\ \omega^{0} & \omega^{1} \end{pmatrix}
= \frac{1}{\sqrt2}\begin{pmatrix}1 & 1\\ 1 & -1\end{pmatrix} = H.
$$

**The Hadamard is the quantum Fourier transform on $\mathbb{Z}_2$.** This is the
deepest view: it is why $H^{\otimes n}$ (a Hadamard on every qubit) creates a
uniform superposition over all $2^n$ inputs, and why Hadamards sit at the heart
of algorithms built on Fourier structure (Deutsch–Jozsa, Simon, Shor).

#### Derivation C — as a sum of Pauli operators

Direct algebra (using the Pauli matrices from §4):

$$
\frac{X + Z}{\sqrt2}
= \frac{1}{\sqrt2}\left[
\begin{pmatrix}0&1\\1&0\end{pmatrix} +
\begin{pmatrix}1&0\\0&-1\end{pmatrix}\right]
= \frac{1}{\sqrt2}\begin{pmatrix}1&1\\1&-1\end{pmatrix} = H.
$$

So $H = (X+Z)/\sqrt2$. This form is the bridge to the physical picture below.

#### Derivation D — as a physical rotation (how hardware makes it)

A rotation of the Bloch sphere by angle $\phi$ about a unit axis
$\hat{n}=(n_x,n_y,n_z)$ is generated by the operator $\hat n\cdot\vec\sigma =
n_x X + n_y Y + n_z Z$:

$$
R_{\hat n}(\phi) = e^{-i\frac{\phi}{2}\,(\hat n\cdot\vec\sigma)}
= \cos\tfrac{\phi}{2}\,I - i\sin\tfrac{\phi}{2}\,(\hat n\cdot\vec\sigma).
$$

(The second equality holds because $(\hat n\cdot\vec\sigma)^2 = I$ for a unit
axis — see §5.) Choose the axis halfway between $x$ and $z$,
$\hat n = (\tfrac{1}{\sqrt2}, 0, \tfrac{1}{\sqrt2})$, so that
$\hat n\cdot\vec\sigma = (X+Z)/\sqrt2$, and rotate by $\phi = \pi$:

$$
R_{\hat n}(\pi) = \cos\tfrac{\pi}{2}\,I - i\sin\tfrac{\pi}{2}\,\frac{X+Z}{\sqrt2}
= -\,i\,\frac{X+Z}{\sqrt2} = -\,i\,H.
$$

Therefore

$$
\boxed{\,H = i\,R_{\hat n}(\pi)\,}
$$

**up to the irrelevant global phase $i$.** In words: *the Hadamard gate is a
$180^\circ$ rotation of the qubit about the diagonal $(\hat x + \hat z)$ axis.*
On real hardware you implement it by driving the qubit with a control pulse
(microwave for superconducting qubits, laser for trapped ions) whose Hamiltonian
is proportional to $\hat n\cdot\vec\sigma$, held on just long enough to sweep out
$180^\circ$. A common elementary decomposition is $H = R_z(\tfrac{\pi}{2})\,
R_x(\tfrac{\pi}{2})\, R_z(\tfrac{\pi}{2})$ (up to global phase), built from the
native $R_x, R_z$ rotations a device already provides.

---

## 4. The Pauli gates

These come from the physics of spin-½ (the original qubit). They are the three
Hermitian, unitary, trace-zero $2\times2$ matrices:

$$
X = \begin{pmatrix}0&1\\1&0\end{pmatrix},\quad
Y = \begin{pmatrix}0&-i\\ i&0\end{pmatrix},\quad
Z = \begin{pmatrix}1&0\\0&-1\end{pmatrix}.
$$

- **$X$ — the quantum NOT (bit flip).** $X|0\rangle = |1\rangle$,
  $X|1\rangle = |0\rangle$. It is the classical NOT extended to superpositions.
- **$Z$ — the phase flip.** $Z|0\rangle = |0\rangle$, $Z|1\rangle = -|1\rangle$.
  It flips the relative phase, turning $|+\rangle \leftrightarrow |-\rangle$.
  It has no classical analogue — it acts purely on phase.
- **$Y$ — a bit *and* phase flip**, $Y = iXZ$.

Key algebraic facts (all check by direct multiplication):

$$
X^2 = Y^2 = Z^2 = I, \qquad
XY = iZ,\; YZ = iX,\; ZX = iY.
$$

Together with $I$ they are a basis for **all** $2\times2$ Hermitian matrices, so
*any* single-qubit Hamiltonian is $\mathcal{H} = aI + b X + c Y + d Z$ with real
coefficients — which is why the Paulis generate every single-qubit gate.

The Hadamard permutes them by conjugation: $HXH = Z$ and $HZH = X$. This is
exactly its "reflection swapping the $x$ and $z$ axes" from Derivation A, now
stated operator-wise — it is why $H$ converts a bit flip into a phase flip and
vice versa, and why measuring after $H$ measures in the $X$ basis.

---

## 5. Rotation gates (the general single-qubit gate)

Exponentiate a Pauli to get a continuous rotation. The trick: because
$\sigma^2 = I$ for any Pauli $\sigma$, the exponential series splits into the
Taylor series of cosine and sine, exactly like Euler's formula:

$$
e^{-i\frac{\theta}{2}\sigma}
= \sum_{k=0}^\infty \frac{1}{k!}\left(-i\tfrac{\theta}{2}\right)^k \sigma^k
= \underbrace{\left(\sum_{\text{even }k}\cdots\right)}_{\cos\frac\theta2}\,I
\;-\; i\,\underbrace{\left(\sum_{\text{odd }k}\cdots\right)}_{\sin\frac\theta2}\,\sigma
= \cos\tfrac{\theta}{2}\,I - i\sin\tfrac{\theta}{2}\,\sigma.
$$

This gives the three elementary rotation gates:

$$
R_x(\theta) = \begin{pmatrix}\cos\frac\theta2 & -i\sin\frac\theta2\\
-i\sin\frac\theta2 & \cos\frac\theta2\end{pmatrix},\quad
R_y(\theta) = \begin{pmatrix}\cos\frac\theta2 & -\sin\frac\theta2\\
\sin\frac\theta2 & \cos\frac\theta2\end{pmatrix},\quad
R_z(\theta) = \begin{pmatrix}e^{-i\theta/2} & 0\\ 0 & e^{i\theta/2}\end{pmatrix}.
$$

Notice the **half-angle** $\theta/2$: rotating the *state vector* by $\theta/2$
rotates the *Bloch sphere* by $\theta$. This factor of two is why a $2\pi$
rotation ($R(2\pi) = -I$) returns the sphere to start but leaves a $-1$ global
phase on the state — the signature of spin-½.

**Phase gates** are special $R_z$'s (up to global phase):

$$
S = \begin{pmatrix}1&0\\0&i\end{pmatrix},\qquad
T = \begin{pmatrix}1&0\\0&e^{i\pi/4}\end{pmatrix},\qquad
Z = S^2,\quad S = T^2.
$$

**Universality of single-qubit gates (Euler / Z–Y–Z decomposition).** *Every*
$2\times2$ unitary $U$ can be written as

$$
U = e^{i\alpha}\,R_z(\beta)\,R_y(\gamma)\,R_z(\delta)
$$

for real angles $\alpha,\beta,\gamma,\delta$. Any single-qubit operation is thus
three fixed-axis rotations and a phase — this is what a compiler emits for
hardware.

---

## 6. Two qubits, entanglement, and CNOT

Two qubits live in the **tensor product** $\mathbb{C}^2\otimes\mathbb{C}^2 =
\mathbb{C}^4$, with basis $|00\rangle, |01\rangle, |10\rangle, |11\rangle$. A
general state has four amplitudes. Applying gate $A$ to the first qubit and $B$
to the second is the gate $A\otimes B$.

The essential entangling gate is **CNOT** (controlled-NOT): if the control (first)
qubit is `1`, flip the target (second); otherwise do nothing.

$$
\text{CNOT} =
\begin{pmatrix}
1&0&0&0\\ 0&1&0&0\\ 0&0&0&1\\ 0&0&1&0
\end{pmatrix}
\qquad
\begin{aligned}
|00\rangle &\mapsto |00\rangle\\
|01\rangle &\mapsto |01\rangle\\
|10\rangle &\mapsto |11\rangle\\
|11\rangle &\mapsto |10\rangle
\end{aligned}
$$

The bottom-right $2\times2$ block is exactly the Pauli $X$ (NOT) acting on the
target — the matrix is just "the logic table, written out."

### Worked example: building a Bell state

Start in $|00\rangle$, apply $H$ to the first qubit, then CNOT:

$$
|00\rangle
\xrightarrow{\;H\otimes I\;}
\frac{|0\rangle+|1\rangle}{\sqrt2}\otimes|0\rangle
= \frac{|00\rangle + |10\rangle}{\sqrt2}
\xrightarrow{\;\text{CNOT}\;}
\frac{|00\rangle + |11\rangle}{\sqrt2}.
$$

The result $\Phi^+ = (|00\rangle+|11\rangle)/\sqrt2$ **cannot be factored** into
(something)$\otimes$(something) — the two qubits are **entangled**. Measure one
and you instantly know the other. This two-line circuit — Hadamard then CNOT —
is the canonical demonstration that superposition ($H$) plus a conditional
coupling (CNOT) produces genuinely quantum correlations.

### Universality

The set $\{H, T, \text{CNOT}\}$ is **universal**: any unitary on any number of
qubits can be approximated to arbitrary accuracy by a circuit of these gates
(Solovay–Kitaev). So the Hadamard is not only the source of superposition — it is
one of the three primitives from which *all* quantum computation is built.

---

## 7. Measurement (closing the loop)

Running a circuit produces a state $|\psi\rangle = \sum_x c_x |x\rangle$. Reading
it out applies the **Born rule**: outcome $x$ appears with probability $|c_x|^2$,
and the state collapses to $|x\rangle$. The art of quantum algorithm design is
arranging, through gates like $H$ that spread amplitude and phases that make it
interfere, for the *wrong* answers' amplitudes to cancel and the *right*
answer's amplitude to build up — so that a single measurement is very likely to
return what you want.

---

## Summary — the through-line

| Concept | Where it comes from |
|---|---|
| State is a unit vector in $\mathbb{C}^2$ | measurement postulate + probabilities sum to 1 |
| Gates are unitary | Schrödinger equation with a Hermitian Hamiltonian |
| Rotation gates $R_x, R_y, R_z$ | $e^{-i\theta\sigma/2}$, using $\sigma^2 = I$ |
| Pauli $X, Y, Z$ | Hermitian/unitary generators of single-qubit dynamics (spin) |
| **Hadamard $H$** | the real self-inverse superposition-maker = reflection = 1-qubit Fourier transform = $(X{+}Z)/\sqrt2$ = $180^\circ$ rotation about $\hat x{+}\hat z$ |
| CNOT + $H$ | superposition + conditional coupling ⇒ entanglement |

The single idea underneath all of it: **a gate is a unitary rotation of the
state vector, generated by turning on a Hamiltonian for a set time**, and the
Hadamard is simply the specific rotation that turns a definite bit into an equal,
phase-carrying superposition.
