---
layout: post
title: "[BLDC Chapter 2] Modeling BLDC motor"
date: 2025-04-03
author: "Hoang Nguyen"
categories: [Tech]
tags: [BLDC]
---
Table of Contents:
* Table of Contents:
{:toc}
---

# I. Voltage Equation

Let us consider a two-pole three-phase BLDC motor as shown in Figure 1.

![image.png](/assets/BLDC-Modeling/image.png)

*Figure 1: Windings of  a two-pole three-phase BLDC motor.*

The voltage equation for the stator windings for a BLDC motor can be expressed as 

$$
\mathbf{v_{abcs}} = \mathbf{R_s} \mathbf{i_{abcs}} + \frac{d \mathbf{\lambda_{abcs}}}{dt} \tag{1}
$$

where the stator voltage $\mathbf{v_{abcs}} = [v_{as}, v_{bs}, v_{cs}]^T$, 
the stator current $\mathbf{i_{abcs}} = [i_{as}, i_{bs}, i_{cs}]^T$, 
the stator flux linkage $\mathbf{\lambda_{abcs}} = [\lambda_{as}, \lambda_{bs}, \lambda_{cs}]^T$, 
and the stator resistance $\mathbf{R_s} = 
\begin{bmatrix} R_s & 0 & 0 \\\ 0 & R_s & 0 \\\ 0 & 0 & R_s \end{bmatrix}.$

The stator linkage flux $\lambda_{abcs}$  consists of $\lambda_{abcs(s)}$  due to the stator current $i_{abcs}$  and $\lambda_{abcs(f)}$ due to the permanent magnet as 

$$
\lambda_{abcs} = \lambda_{abcs(s)} + \lambda_{abcs(f)}   \tag{2}
$$

Substituting (2) to (1) we have

$$
\mathbf{v}_{abcs} = \mathbf{R_s} \mathbf{i}_{abcs} + \frac{d \boldsymbol{\lambda}_{abcs}}{dt} = \mathbf{R_s} \mathbf{i}_{abcs} + \frac{d \boldsymbol{\lambda}_{abc(s)}}{dt} + \mathbf{e}_{abcs} \tag{3} 
$$

where the back EMF due to the permanent magnet can be expressed as $\mathbf{e_{abcs}} = \frac{d \boldsymbol{\lambda}_{abc(f)}}{dt}$ and also given as 

$$
\boldsymbol{e}_{abcs} = 
\begin{bmatrix} 
e_{as} \\ 
e_{bs} \\ 
e_{cs} 
\end{bmatrix} 
= \omega_m
\begin{bmatrix} 
\lambda_{asf} \\ 
\lambda_{bsf} \\ 
\lambda_{csf} 
\end{bmatrix} 
= \omega_m \lambda_f
\begin{bmatrix} 
f(\theta_r) \\ 
f(\theta_r - 120^\circ) \\ 
f(\theta_r - 240^\circ) 
\end{bmatrix} \tag{4}
$$

where $\lambda_{f} (= N\phi_f)$  is the amount of magnet flux $\phi_f$  linking N turns of stator windings, $f(\theta_r)$ is the unit function representing the waveform of the back EMF, $\theta_r$  is the rotor position and $\omega_m$  is the rotor velocity.

The unit function $f(\theta_r)$ for the trapezoidal back-EMF waveform of the BLDC motor can be expressed as

$$
f(\theta_r) = \begin{cases} \frac{6\theta_r}{\pi} & (0 \leq \theta_r < \pi/6) \\ 1 & (\pi/6 \leq \theta_r < 5\pi/6) \\ -\frac{6\theta_r}{\pi} + 6 & (5\pi/6 \leq \theta_r < 7\pi/6) \\ -1 & (7\pi/6 \leq \theta_r < 11\pi/6) \\ \frac{6\theta_r}{\pi} - 12 & (11\pi/6 \leq \theta_r < 2\pi) \end{cases} \tag{5}
$$

The stator flux linkage $\lambda_{abcs(s)}$  due to the stator current is given by

$$
\boldsymbol{\lambda}_{abcs(s)} = \boldsymbol{L}_s i_{abcs} = \begin{bmatrix}L_{aa} & L_{ab} & L_{ac} \\L_{ba} & L_{bb} & L_{bc} \\L_{ca} & L_{cb} & L_{cc}\end{bmatrix}\begin{bmatrix}i_{as} \\i_{bs} \\i_{cs}\end{bmatrix} \tag{6}
$$

For the symmetry three-phase winding, the self-inductances are all the same and the mutual inductances are all the same as

$$
L_{aa} = L_{bb} = L_{cc} = L_s = L_{ls} + L_m \tag{7}
$$

$$
L_{ab} = L_{ac} = L_{ba} = L_{bc} = L_{ca} = L_{cb} = -\frac{1}{2}L_m = M \tag{8}
$$

where $L_{\alpha\beta} = \lambda/i_\beta$ expresses the winding inductance, which is the ratio of the linkage flux $\lambda$  of the winding $\alpha$  to the current $i_\beta$  that produces the flux, $L_{ls}$  is the leakage inductance, $L_m$  is the magnetizing inductance.

From (6) to (8), the stator voltage equation can be rewritten as

$$
v_{abcs} = R_s i_{abcs} + L_{abcs} \frac{di_{abc(s)}}{dt} + e_{abcs} \tag{9}
$$

$$
\begin{bmatrix}v_{as} \\v_{bs} \\v_{cs}\end{bmatrix}=\begin{bmatrix}R_s & 0 & 0 \\0 & R_s & 0 \\0 & 0 & R_s\end{bmatrix}\begin{bmatrix}i_{as} \\i_{bs} \\i_{cs}\end{bmatrix}+\begin{bmatrix}L_s & M & M \\M & L_s & M \\M & M & L_s\end{bmatrix}\frac{d}{dt}\begin{bmatrix}i_{as} \\i_{bs} \\i_{cs}\end{bmatrix}+\begin{bmatrix}e_{as} \\e_{bs} \\e_{cs}\end{bmatrix} \tag{10}
$$

here, since $i_{as} + i_{bs} + i_{cs} = 0$, the middle term of (10) can be reduced as 

$$
\frac{d}{dt}[L_s i_{as} + M i_{bs} + M i_{cs}] = \frac{d}{dt}[L_s i_{as} - M i_{as}] \tag{11}
$$

thus (10) can be rewritten as 

$$
\begin{bmatrix} v_{as} \\ v_{bs} \\ v_{cs} \end{bmatrix} = \begin{bmatrix} R_s & 0 & 0 \\ 0 & R_s & 0 \\ 0 & 0 & R_s \end{bmatrix} \begin{bmatrix} i_{as} \\ i_{bs} \\ i_{cs} \end{bmatrix} + \begin{bmatrix} L_s - M & 0 & 0 \\ 0 & L_s - M & 0 \\ 0 & 0 & L_s - M \end{bmatrix} \frac{d}{dt} \begin{bmatrix} i_{as} \\ i_{bs} \\ i_{cs} \end{bmatrix} + \begin{bmatrix} e_{as} \\ e_{bs} \\ e_{cs} \end{bmatrix} \tag{12}
$$

---

# II. Torque equation

The output torque of a three-phase motor is generally calculated from the output power $P_e$  and the mechanical angular velocity $\omega_m$  as 

$$
P_{e} = e_{as} i_{as} + e_{bs} i_{bs} + e_{cs} i_{cs} \tag{13}
$$

$$
T_{e} = \frac{P_{e}}{\omega_{m}} = \frac{e_{as} i_{as} + e_{bs} i_{bs} + e_{cs} i_{cs}}{\omega_{m}} \tag{14}
$$

where $\omega_m = \omega_e/p$, with $\omega_e$  is electrical angular velocity, $p$  is the number of the poles.

As seen from torque equation, the phase current is needed to be in phase with the back-EMF to produce the maximum torque. 

# References

[1] S.-H. Kim, *Electric Motor Control: DC, AC, and BLDC Motors*. Amsterdam, Netherlands: Elsevier, 2017, pp. 1–426.