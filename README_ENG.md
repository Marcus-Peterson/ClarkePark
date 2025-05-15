**Translation plan**

The user requests a high-quality translation from Spanish to English using established techniques and international standards. The content includes code, headings, and images, so I need to preserve the markdown structure. I'll provide clear and thorough translation while keeping minimal fluff and avoiding emojis. I'll make sure to translate headings, bullet points, and paragraphs to ensure clarity. The approach should be straightforward and well-structured to meet the user's preferences. I'll now begin the translation.

**Handling code in translation**

I need to think about how to handle code versus explanatory text for translation. The user didn’t ask for changes to the code itself, but mentioning "translate correctly" makes me wonder if even code comments or labels should be translated. For labels like "Sistema trifásico ABC" or "Tiempo \[Segundos]," it makes sense to translate them but keep the code structure intact.  If the user wants everything translated, I’ll annotate the translation and leave clear the sections where code is untouched except for labels and comments.

Thought for 20 seconds

[![PyPI version](https://i.ibb.co/VVVC5z4/2022-01-10-00-20-11-2.jpg)](https://pypi.org/project/ClarkePark/)

[![PyPI version](https://badge.fury.io/py/ClarkePark.svg)](https://badge.fury.io/py/ClarkePark)
[![Python versions supported by ClarkePark](https://img.shields.io/badge/python-3%20%7C%203.5%20%7C%203.6%20%7C%203.7%20%7C%203.8%20%7C%203.9%20%7C%203.10-blue)](https://pypi.org/project/ClarkePark/)
[![Total downloads](https://pepy.tech/badge/clarkepark)](https://pepy.tech/project/clarkepark)
[![Monthly downloads](https://pepy.tech/badge/clarkepark/month)](https://pepy.tech/project/clarkepark)
[![Maintainability](https://api.codeclimate.com/v1/badges/6abceb2a140780c13d17/maintainability)](https://codeclimate.com/github/jacometoss/ClarkePark/maintainability)

# Park & Clarke Transformation

The **Park (dq0) & Clarke (α, β)** library provides the following modules:

* **Clarke forward transform** – converts the three time-domain phase components *A*, *B*, *C* to the stationary orthogonal reference frame α, β.
* **Clarke inverse transform** – converts stationary orthogonal components α, β back to time-domain phase components *A*, *B*, *C*.
* **Park forward transform** – converts the ABC frame to the rotating dq0 reference frame under steady-state conditions.
* **Park inverse transform** – converts rotating dq0 components back to the time-domain ABC frame.
* **Stationary-to-rotating conversion** – converts the stationary orthogonal frame α, β to the rotating dq0 frame.

## Installation

Install the package with:

```bash
pip install ClarkePark
```

---

## Transformation (a, b, c) → (α, β)

The module depends on **NumPy** for numerical operations and **Matplotlib** for plotting results.

```python
alpha, beta, zero = ClarkePark.abc_to_alphaBeta0(A, B, C)
```

Before calling the transform, generate three balanced, phase-shifted single-phase signals:

```python
import ClarkePark
import numpy as np
import matplotlib.pyplot as plt

end_time = 10 / 60        # 10 electrical cycles at 60 Hz
step_size = end_time / 1000
t = np.arange(0, end_time, step_size)
wt = 2 * np.pi * 60 * t    # electrical angle

rad_angA = 0 * np.pi / 180
rad_angB = 240 * np.pi / 180
rad_angC = 120 * np.pi / 180

A = np.sqrt(2) * 127 * np.sin(wt + rad_angA)
B = np.sqrt(2) * 127 * np.sin(wt + rad_angB)
C = np.sqrt(2) * 127 * np.sin(wt + rad_angC)

alpha, beta, zero = ClarkePark.abc_to_alphaBeta0(A, B, C)
```

Plotting the phase voltages **A, B, C**

![ABC](https://i.ibb.co/59wxgbm/02.jpg)

Plotting the stationary reference frame **(α, β)**

<img src="https://i.ibb.co/gz1krwx/01.jpg" alt="Clarke frame" />

---

## Transformation (A B C) → (dq0)

Applying the same signals, the Park transform is obtained with

```python
d, q, zero = ClarkePark.abc_to_dq0(A, B, C, wt, delta)
```

A rotating three-phase system expressed in dq0 yields two constant-value signals under steady-state conditions:

<img src="https://i.ibb.co/MB3Mk68/03.jpg" alt="dq0 frame"  />

---

## Inverse transformation (dq0) → (A B C)

```python
a, b, c = ClarkePark.dq0_to_abc(d, q, zero, wt, delta)
```

Starting from the constant dq0 frame, we can return to the sinusoidal ABC system.

```python
import ClarkePark
import numpy as np
import matplotlib.pyplot as plt

end_time = 3 / 60
step_size = end_time / 1000
delta = 0
t = np.arange(0, end_time, step_size)
wt = 2 * np.pi * 60 * t

rad_angA = 0 * np.pi / 180
rad_angB = 240 * np.pi / 180
rad_angC = 120 * np.pi / 180

A = np.sqrt(2) * 127 * np.sin(wt + rad_angA)
B = np.sqrt(2) * 127 * np.sin(wt + rad_angB)
C = np.sqrt(2) * 127 * np.sin(wt + rad_angC)

d, q, zero = ClarkePark.abc_to_dq0(A, B, C, wt, delta)
a, b, c = ClarkePark.dq0_to_abc(d, q, zero, wt, delta)
```

Plot the reconstructed three-phase system:

```python
plt.figure(figsize=(8, 3))
plt.plot(t, a, label="A", color="royalblue")
plt.plot(t, b, label="B", color="orangered")
plt.plot(t, c, label="C", color="forestgreen")
plt.legend(ncol=3, loc=4)
plt.ylabel("Voltage [V]")
plt.xlabel("Time [s]")
plt.title("Three-phase system ABC")
plt.grid(True)
plt.show()
```

![dq0\_to\_abc](https://i.ibb.co/gtWbCj7/Figure-2.png)

---

## Transformation (α, β) → (dq0)

```python
d, q, zero = ClarkePark.alphaBeta0_to_dq0(alpha, beta, zero, wt, delta)
```

From the stationary orthogonal frame α, β we can obtain the rotating dq0 frame.
Using the previous code, simply add:

```python
alpha, beta, zero = ClarkePark.abc_to_alphaBeta0(a, b, c)
d, q, zero = ClarkePark.alphaBeta0_to_dq0(alpha, beta, zero, wt, delta)
```

The resulting dq0 values are identical to those obtained with the direct ABC → dq0 transform.

---

## Developer & Version

```text
[Package] : ClarkePark 0.1.7
[Author]  : Marco Polo Jácome Toss
[Blog]    : https://k-denveloper.blogspot.com/
[License] : GNU General Public License v3.0
[Date]    : 10 January 2021
[Country] : Mexico
```

---

## Changelog

* **0.1.7** – Module documentation and input-validation improvements (10 Jan 2022)

---

## References

1. Kundur, P. (1994). *Power System Stability and Control.* McGraw-Hill.
2. J. C. Das (2016). *Understanding Symmetrical Components for Power System Modeling.* IEEE Press.

---

## Registration Information

<a href="https://www.safecreative.org/work/2207301699317" xmlns:cc="http://creativecommons.org/ns#" rel="cc:license"><img src="https://resources.safecreative.org/work/2207301699317/label/standard2-150" style="border:0;" alt="Safe Creative #2207301699317"/></a>
