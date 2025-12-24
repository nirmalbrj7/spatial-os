# RFC-0001: Core Concepts and Scope of Spatial OS

**Status:** Draft  
**Created:** 2025-12-24  
**Authors:** Spatial OS Maintainers  
**Applies to:** Spatial OS v1.x  

---

## 1. Purpose

This RFC defines the **core concepts, terminology, and non-goals** of Spatial OS.  
All future development, APIs, SDKs, and integrations **MUST conform** to the definitions in this document.

This RFC is intentionally conservative.  
It prioritizes **long-term stability, interoperability, and neutrality** over short-term features.

---

## 2. Problem Summary

Spatial computing systems today are fragmented:

- Spatial anchors are siloed inside applications and vendor platforms
- Persistence is app-specific and device-specific
- There is no shared spatial substrate across AR/VR ecosystems
- Developers repeatedly re-implement spatial persistence logic
- Enterprises and researchers face vendor lock-in and non-reproducibility

Spatial OS exists to solve **only the infrastructure layer of this problem**.

---

## 3. Design Principles

Spatial OS is guided by the following principles:

1. **Infrastructure over applications**  
   Spatial OS provides primitives, not experiences.

2. **Neutrality**  
   Spatial OS does not favor devices, engines, vendors, or platforms.

3. **Separation of concerns**  
   - Tracking and mapping are device responsibilities  
   - Persistence, permissions, and synchronization are Spatial OS responsibilities

4. **Minimalism**  
   Spatial OS defines the smallest viable set of concepts required for shared reality.

5. **Long-term stability**  
   Breaking changes are rare and require RFC approval.

---

## 4. Core Concepts (Normative)

### 4.1 Space

A **Space** is a logical container representing a persistent real-world context.

Examples:
- A room
- A building
- A campus
- A zone within a city

**Properties:**
- Identified by a globally unique `space_id`
- May optionally include coarse geographic hints
- Contains anchors, layers, and coordinate frames

**Important notes:**
- A Space is **not** a map
- A Space is **not** a SLAM mesh
- A Space is **not** owned by a device

A Space exists independently of any application or session.

---

### 4.2 Anchor

An **Anchor** is a persistent spatial entity associated with a Space.

An Anchor represents **“something that exists at a location in space.”**

Examples:
- A note
- A label
- A waypoint
- A narrative marker
- Any future spatial object defined by applications

**Properties:**
- Unique `anchor_id`
- Belongs to exactly one Space
- Has a canonical pose expressed in the Space coordinate frame
- Has a confidence value
- Has ownership and permissions
- Contains an opaque `payload`

**Important notes:**
- Spatial OS does not interpret anchor payloads
- Spatial OS does not render anchors
- Anchor types are extensible but not standardized in v1

---

### 4.3 Layer

A **Layer** is a logical grouping of anchors within a Space.

Layers enable:
- Visibility control
- Permission control
- Conceptual separation of concerns

Examples:
- Public layer
- Private layer
- Research layer
- Experimental layer

**Properties:**
- Identified by `layer_id`
- Belongs to exactly one Space
- Defines default read/write permissions
- Does not impose semantics on anchor content

Layers do **not** define ordering, priority, or rendering rules.

---

### 4.4 Frame (Coordinate Frame)

A **Frame** is a coordinate reference used to relate device poses to a Space.

**Types:**
- Space frame (canonical)
- Device frames (ephemeral)

**Properties:**
- Defines a transform relative to the Space frame
- Used to convert device-local poses into Space-relative poses

**Important notes:**
- Spatial OS does not implement SLAM
- Frames allow Spatial OS to remain device-agnostic
- Frames may be updated over time to improve alignment

---

### 4.5 Principal (Identity)

A **Principal** is an entity capable of performing actions.

Examples:
- A human user
- A device
- An application

**Properties:**
- Identified by `principal_id`
- Assigned permissions
- Can own spaces and anchors

Spatial OS treats all principals uniformly.

---

### 4.6 Permissions

Spatial OS enforces access control at multiple levels:

- Space
- Layer
- Anchor

Permissions include:
- Read
- Write
- Admin

Default permissions may be overridden by explicit ACLs.

---

## 5. Synchronization Model

Spatial OS provides **state synchronization**, not behavior synchronization.

- Anchor creation, updates, and deletion events are propagated
- Spatial OS does not define animation, interaction, or UI behavior
- Versioning is used to ensure consistency

Real-time synchronization is **best-effort**, not hard-real-time.

---

## 6. Explicit Non-Goals

Spatial OS explicitly does **NOT** attempt to:

- Perform SLAM or environment reconstruction
- Store raw spatial meshes or camera imagery
- Render 3D content
- Define user interfaces or experiences
- Act as a social network
- Act as a content marketplace
- Replace game engines or headset operating systems

Any proposal that violates these non-goals **must be rejected**.

---

## 7. Compatibility & Extensibility

Spatial OS is designed to be:

- Engine-agnostic (Unity, Unreal, WebXR)
- Device-agnostic (OpenXR-compatible abstraction)
- Payload-agnostic (future use cases without protocol changes)

Extensions must:
- Preserve backward compatibility
- Be proposed via RFC
- Avoid coupling to specific vendors or platforms

---

## 8. Security & Privacy Baseline

- No raw environment capture is stored by default
- All access is permissioned
- Sensitive operations are auditable
- Spatial OS stores **positions**, not **perception data**

Privacy is a **foundational constraint**, not an optional feature.

---

## 9. Versioning Policy

- This RFC applies to Spatial OS v1.x
- Any change to definitions in Sections 4–6 requires:
  - A new RFC
  - Major version increment if breaking

---

## 10. Rationale

Spatial OS is designed to become **invisible infrastructure**.

Its success is measured not by user interfaces, but by:
- How many ecosystems depend on it
- How rarely it needs to change
- How well it enables others to innovate

---

## 11. Status

This RFC is currently **Draft**.

Once accepted:
- It becomes binding for all implementations
- It may only be superseded by a future RFC

---
