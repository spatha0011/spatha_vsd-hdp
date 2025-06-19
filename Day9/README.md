# VSD Hardware Design Program

## Velocity Saturation and basics of CMOS inverter VTC

### SPICE simulation for lower nodes

The plot below shows the output characteristics of an NMOS device with W=1.8μm, L=1.2μm (W/L = 1.5).

![Alt Text](Images/1.png)

- **Linear Region:**  
  The drain current (Id) is a linear function of Vds in this region.  
  It is defined for Vds < (Vgs - Vt).

- **Saturation Region:**  
  The drain current (Id) depends on channel length modulation and Vds.  
  It is defined for Vds ≥ (Vgs - Vt).

**Explanation:**  
- The region **before** Vds = Vgs - Vt is the **Linear Region**, where Id varies linearly with Vds.  
- The region **after** Vds = Vgs - Vt is the **Saturation Region**, where Id is influenced by channel length modulation and Vds.

**Formulas:**  
**Linear region:**  
_Id = kn ⋅ [(Vgs - Vt)Vds - (Vds² / 2)]_

**Saturation region:**  
_Id = (kn / 2) ⋅ (W/L) ⋅ (Vgs - Vt)² ⋅ [1 + λVds]_

---

### Observation 1: Long Channel vs. Short Channel NMOS Characteristics

The plot below compares NMOS output characteristics for long channel and short channel devices with **same W/L ratio**:

![Alt Text](Images/2.png)

**Description:**

In this figure:

- **Left Plot**: W = 1.8μm, L = 1.2μm device → Long channel device
- **Right Plot**: W = 0.375μm, L = 0.25μm device → Short channel device  
  (Since channel length < 0.25μm, it is considered a short channel device)

**Key Observations:**

- Both devices have the **same W/L ratio**, but different Width (W) and Length (L).
- **Id behavior:**
  - For the **long channel device**:  
    Id is a **quadratic function of Vgs**.
  - For the **short channel device**:  
    Id is **quadratic at low Vgs**, but becomes more **linear at high Vgs**.

To observe this behavior, we apply a constant Vds and sweep Vgs:

- In long channel devices, Id follows the ideal quadratic dependence on Vgs.
- In short channel devices:
  - At **low Vgs**, the device behaves similarly to long channel — quadratic Id-Vgs.
  - At **high Vgs**, the Id-Vgs curve becomes linear due to **velocity saturation**.
    - This velocity saturation occurs because the carrier velocity reaches a maximum limit at high electric fields in short channel devices.

Thus, this plot demonstrates how **velocity saturation** affects Id in short channel devices — transitioning the behavior from quadratic to linear at high Vgs.

![Alt Text](Images/3.png)

For long-channel devices, drain current shows a quadratic dependence on gate voltage.

For short-channel devices, it is quadratic at low gate voltage but becomes linear at higher voltages due to velocity saturation.

![Alt Text](Images/4.png)

