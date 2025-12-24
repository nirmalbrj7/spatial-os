Here is the complete, consolidated Markdown file (`schemas.md`) based on your specifications. I have cleaned up the formatting, standardized the tables, and included the "Next Steps" section at the end.

---

# Spatial OS – Data Schemas (v1)

**Status:** Draft

**Applies to:** Spatial OS v1.x

**Depends on:** RFC-0001 (Core Concepts)

This document defines the **canonical data schemas** used by Spatial OS. These schemas form the **public contract** between the Spatial OS core, SDKs, applications, and integrations.

All implementations **MUST** conform to these schemas.

---

## 1. Design Rules

1. **Engine-agnostic and device-agnostic**: Schemas do not favor specific game engines (Unity/Unreal) or hardware (Quest/VisionPro).
2. **Spatial Persistence**: Schemas store long-term data, not per-frame perception/tracking data.
3. **Extensibility**: Schemas allow new fields without breaking existing integrations.
4. **Opaque Payloads**: Spatial OS manages the "where" and "who"; the application manages the "what" via opaque blobs.
5. **Unique Identifiers**: All IDs are UUID v4.

---

## 2. Identifier Conventions

All primary entities use UUIDs.

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000"
}

```

---

## 3. Pose Schema

The Pose schema represents a rigid transform in 3D space.

### 3.1 Pose (Required)

```json
{
  "p": [0.0, 1.2, -3.4],
  "q": [0.0, 0.0, 0.0, 1.0]
}

```

| Field | Type | Description |
| --- | --- | --- |
| `p` | number[3] | Position (x, y, z) in meters |
| `q` | number[4] | Quaternion (x, y, z, w) |

**Notes:** * Units are meters.

* Coordinate handedness is implementation-defined but must be consistent per Space.

### 3.2 Pose With Confidence

```json
{
  "p": [0.0, 1.2, -3.4],
  "q": [0.0, 0.0, 0.0, 1.0],
  "confidence": 0.85
}

```

| Field | Type | Description |
| --- | --- | --- |
| `confidence` | number (0–1) | Alignment/Tracking confidence score |

---

## 4. Principal Schema

A Principal represents an identity (User, Device, or App) that can perform actions.

```json
{
  "principal_id": "uuid",
  "principal_type": "user | device | app",
  "display_name": "string",
  "created_at": "ISO-8601 timestamp"
}

```

---

## 5. Space Schema

A Space is the top-level persistent logical container (e.g., a room or a building).

```json
{
  "space_id": "uuid",
  "name": "Dalhousie AR Lab",
  "owner_principal_id": "uuid",
  "geo_hint": {
    "lat": 44.637,
    "lon": -63.591,
    "radius_m": 30
  },
  "metadata": {},
  "created_at": "ISO-8601 timestamp",
  "updated_at": "ISO-8601 timestamp"
}

```

* **geo_hint**: Optional and non-authoritative. Used for discovery.
* **Geofencing**: Spatial OS does not enforce geofencing at the schema level.

---

## 6. Frame Schema

A Frame defines a coordinate reference (e.g., a specific SLAM map or origin point).

```json
{
  "frame_id": "uuid",
  "space_id": "uuid",
  "frame_type": "space | device",
  "label": "Quest3Tracking",
  "t_space_from_frame": {
    "p": [0.0, 0.0, 0.0],
    "q": [0.0, 0.0, 0.0, 1.0],
    "confidence": 0.7
  },
  "created_at": "ISO-8601 timestamp",
  "updated_at": "ISO-8601 timestamp"
}

```

---

## 7. Layer Schema

A Layer groups anchors for the purpose of managing permissions and visibility at scale.

```json
{
  "layer_id": "uuid",
  "space_id": "uuid",
  "name": "public",
  "is_public_read": true,
  "is_public_write": false,
  "metadata": {},
  "created_at": "ISO-8601 timestamp"
}

```

---

## 8. Anchor Schema

An Anchor is the core persistent entity representing a point of interest.

```json
{
  "anchor_id": "uuid",
  "space_id": "uuid",
  "layer_id": "uuid",
  "owner_principal_id": "uuid",
  "anchor_type": "generic",
  "pose_space": {
    "p": [0.0, 1.2, -3.4],
    "q": [0.0, 0.0, 0.0, 1.0]
  },
  "confidence": 0.92,
  "payload": {},
  "status": "active | pending | archived | deleted",
  "version": 3,
  "created_at": "ISO-8601 timestamp",
  "updated_at": "ISO-8601 timestamp"
}

```

---

## 9. Anchor ACL Schema

Fine-grained permissions that override layer-level defaults.

```json
{
  "anchor_id": "uuid",
  "subject_type": "principal | group",
  "subject_id": "uuid",
  "can_read": true,
  "can_write": false,
  "can_admin": false,
  "created_at": "ISO-8601 timestamp"
}

```

---

## 10. Group Schema

Used for managing collections of Principals.

```json
{
  "group_id": "uuid",
  "name": "ResearchTeam",
  "created_at": "ISO-8601 timestamp"
}

```

---

## 11. Audit Event Schema

Provides a full historical trail of actions within a Space.

```json
{
  "event_id": "uuid",
  "occurred_at": "ISO-8601 timestamp",
  "actor_principal_id": "uuid",
  "action": "anchor.create",
  "space_id": "uuid",
  "anchor_id": "uuid",
  "metadata": {}
}

```

---

## 12. Schema Stability Rules

1. New optional fields **MAY** be added to any schema.
2. Existing fields **MUST NOT** change their semantic meaning.
3. Removing fields or changing types requires a major version bump (e.g., v1 to v2).
4. Breaking changes require a formal RFC.

---

## 13. Relationship Summary

* **Principal** ── owns ──> **Space**
* **Space** ── contains ──> **Layers**
* **Space** ── contains ──> **Frames**
* **Layer** ── contains ──> **Anchors**
* **Anchor** ── has ──> **ACLs**
* **Principal/Group** ── referenced by ──> **ACLs**

---

Here is the updated final section of the document, incorporating the status and the critical path for the project.

---

## 14. Status

**This document is currently in Draft status.**

Once this schema definition is accepted:

* It serves as the **authoritative data contract** for Spatial OS.
* All internal APIs, external SDKs (Unity, Web, C++), and data storage layers **MUST** conform to these definitions.
* Any deviation will require a formal versioning update.

---