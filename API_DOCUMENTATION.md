# Bidflow API Documentation

**Version:** v1  
**Base URL:** `https://yourdomain.com/api/v1`  
**Format:** JSON  
**Authentication:** Bearer Token (JWT) — except Planroom (token-based) and Webhooks (HMAC)

---

## Table of Contents

1. [Dashboard](#1-dashboard)
2. [Projects](#2-projects)
3. [Plans & Sheets](#3-plans--sheets)
4. [Specs](#4-specs)
5. [Scopes](#5-scopes)
6. [Takeoffs](#6-takeoffs)
7. [Bid Packages](#7-bid-packages)
8. [Subcontractors (CRM)](#8-subcontractors-crm)
9. [Contacts](#9-contacts)
10. [Invitations](#10-invitations)
11. [Sub Planroom (No-Login)](#11-sub-planroom-no-login)
12. [Bids](#12-bids)
13. [Bid Leveling & Award](#13-bid-leveling--award)


---

## Global Headers

```
Content-Type: application/json
Accept: application/json
Authorization: Bearer {token}
```

## Standard Response Format

### Success
```json
{
  "success": true,
  "data": {},
  "message": "Operation successful"
}
```

### Error
```json
{
  "success": false,
  "message": "Error description",
  "errors": {
    "field": ["Validation error message"]
  }
}
```

### Paginated List
```json
{
  "success": true,
  "data": [],
  "meta": {
    "current_page": 1,
    "per_page": 20,
    "total": 100,
    "last_page": 5
  }
}
```

---

## 1. Dashboard

### GET `/dashboard`
Get dashboard summary including stats, alerts, and pending tasks.

**Auth Required:** Yes

**Query Parameters:**

| Parameter | Type   | Required | Description          |
|-----------|--------|----------|----------------------|
| `org_id`  | integer | No      | Filter by org        |

**Response `200`:**
```json
{
  "success": true,
  "data": {
    "stats": {
      "active_projects": 12,
      "open_packages": 5,
      "pending_tasks": 8,
      "bids_received_today": 3
    },
    "alerts": [
      {
        "type": "coverage_gap",
        "message": "Electrical package has less than 3 bids",
        "package_id": 42
      }
    ],
    "recent_activity": []
  }
}
```

---

## 2. Projects

### GET `/projects`
List all projects.

**Auth Required:** Yes

**Query Parameters:**

| Parameter | Type    | Required | Description                              |
|-----------|---------|----------|------------------------------------------|
| `stage`   | string  | No       | Filter by stage (active, closed, archive)|
| `search`  | string  | No       | Search by project name                   |
| `page`    | integer | No       | Page number (default: 1)                 |
| `per_page`| integer | No       | Results per page (default: 20)           |

**Response `200`:**
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "name": "Westside Medical Tower",
      "stage": "active",
      "budget": 15000000,
      "procore_id": null,
      "created_at": "2026-01-15T10:00:00Z"
    }
  ],
  "meta": { "current_page": 1, "total": 25 }
}
```

---

### GET `/projects/{id}`
Get a single project's details.

**Auth Required:** Yes

**Path Parameters:**

| Parameter | Type    | Description  |
|-----------|---------|--------------|
| `id`      | integer | Project ID   |

**Response `200`:**
```json
{
  "success": true,
  "data": {
    "id": 1,
    "name": "Westside Medical Tower",
    "stage": "active",
    "budget": 15000000,
    "location": "Los Angeles, CA",
    "procore_id": null,
    "members": [],
    "packages_count": 3,
    "created_at": "2026-01-15T10:00:00Z"
  }
}
```

---

### POST `/projects/{id}/link/procore`
Link a project to Procore.

**Auth Required:** Yes | **Role:** admin, sr_estimator

**Request Body:**
```json
{
  "procore_project_id": "98765"
}
```

**Response `200`:**
```json
{
  "success": true,
  "data": {
    "project_id": 1,
    "procore_id": "98765",
    "linked_at": "2026-05-13T10:00:00Z"
  },
  "message": "Project linked to Procore"
}
```

---

## 3. Plans & Sheets

### GET `/projects/{id}/sheets`
List all sheets (plan pages) for a project.

**Auth Required:** Yes

**Query Parameters:**

| Parameter    | Type   | Required | Description                     |
|--------------|--------|----------|---------------------------------|
| `discipline` | string | No       | Filter by discipline (Elec, Mech)|
| `page`       | integer| No       | Page number                     |

**Response `200`:**
```json
{
  "success": true,
  "data": [
    {
      "id": 10,
      "project_id": 1,
      "filename": "E-101.pdf",
      "discipline": "Electrical",
      "page_number": 1,
      "ai_labels": ["panel", "conduit", "lighting"],
      "uploaded_at": "2026-05-01T09:00:00Z"
    }
  ]
}
```

---

### GET `/sheets/{id}`
Get sheet detail with AI metadata.

**Auth Required:** Yes

**Response `200`:**
```json
{
  "success": true,
  "data": {
    "id": 10,
    "filename": "E-101.pdf",
    "discipline": "Electrical",
    "ai_labels": ["panel", "conduit"],
    "page_number": 1,
    "thumbnail_url": "https://storage.example.com/sheets/10/thumb.jpg",
    "file_url": "https://storage.example.com/sheets/10/full.pdf"
  }
}
```

---

## 4. Specs

### GET `/projects/{id}/specs`
List specification sections (CSI MasterFormat) for a project.

**Auth Required:** Yes

**Query Parameters:**

| Parameter | Type   | Required | Description          |
|-----------|--------|----------|----------------------|
| `search`  | string | No       | Full-text search     |
| `division`| string | No       | CSI division number  |

**Response `200`:**
```json
{
  "success": true,
  "data": [
    {
      "id": 5,
      "section_number": "16050",
      "title": "Basic Electrical Materials and Methods",
      "content_preview": "This section covers...",
      "project_id": 1
    }
  ]
}
```

---

### GET `/specs/{id}`
Get a single spec section's full detail.

**Auth Required:** Yes

**Response `200`:**
```json
{
  "success": true,
  "data": {
    "id": 5,
    "section_number": "16050",
    "title": "Basic Electrical Materials and Methods",
    "content": "Full spec text...",
    "created_at": "2026-05-01T09:00:00Z"
  }
}
```

---

## 5. Scopes

### GET `/projects/{id}/scopes`
List all scopes for a project.

**Auth Required:** Yes

**Query Parameters:**

| Parameter | Type   | Required | Description                             |
|-----------|--------|----------|-----------------------------------------|
| `status`  | string | No       | draft, in_review, approved, superseded  |
| `trade`   | string | No       | Filter by trade                         |

**Response `200`:**
```json
{
  "success": true,
  "data": [
    {
      "id": 20,
      "trade": "Electrical",
      "title": "Electrical Scope - WMT",
      "status": "approved",
      "version": 2,
      "approved_by": "Jane Smith",
      "approved_at": "2026-04-20T14:00:00Z"
    }
  ]
}
```

---

### GET `/scopes/{id}`
Get scope detail with full content.

**Auth Required:** Yes

**Response `200`:**
```json
{
  "success": true,
  "data": {
    "id": 20,
    "trade": "Electrical",
    "title": "Electrical Scope - WMT",
    "description": "Full scope of work...",
    "status": "approved",
    "version": 2,
    "sheet_refs": [10, 11],
    "spec_refs": [5],
    "created_at": "2026-04-01T10:00:00Z"
  }
}
```

---

### PATCH `/scopes/{id}`
Update a scope (only allowed when status is draft or in_review).

**Auth Required:** Yes | **Role:** estimator, sr_estimator

**Request Body:**
```json
{
  "description": "Updated scope of work...",
  "status": "in_review"
}
```

**Response `200`:**
```json
{
  "success": true,
  "data": { "id": 20, "status": "in_review" },
  "message": "Scope updated"
}
```

---

### POST `/scopes/{id}/approve`
Approve a scope (creates a locked version).

**Auth Required:** Yes | **Role:** sr_estimator, admin

**Response `200`:**
```json
{
  "success": true,
  "data": { "id": 20, "status": "approved", "version": 2 },
  "message": "Scope approved"
}
```

---

## 6. Takeoffs

### GET `/projects/{id}/takeoffs`
List all takeoffs for a project.

**Auth Required:** Yes

**Response `200`:**
```json
{
  "success": true,
  "data": [
    {
      "id": 600,
      "scope_id": 20,
      "name": "Outlet Count - Floor 3",
      "trade": "Electrical",
      "total_quantity": 142,
      "created_at": "2026-04-15T10:00:00Z"
    }
  ]
}
```

---

### GET `/takeoffs/{id}`
Get takeoff detail with annotations.

**Auth Required:** Yes

**Response `200`:**
```json
{
  "success": true,
  "data": {
    "id": 600,
    "name": "Outlet Count - Floor 3",
    "total_quantity": 142,
    "annotations": [
      {
        "sheet_id": 10,
        "type": "count",
        "label": "duplex outlet",
        "count": 78,
        "marks": [{ "x": 120, "y": 340 }]
      }
    ]
  }
}
```

---

## 7. Bid Packages

### GET `/projects/{id}/packages`
List all bid packages for a project.

**Auth Required:** Yes

**Query Parameters:**

| Parameter | Type   | Required | Description                                      |
|-----------|--------|----------|--------------------------------------------------|
| `status`  | string | No       | draft, sent, open, closed, leveled, awarded      |
| `trade`   | string | No       | Filter by trade                                  |

**Response `200`:**
```json
{
  "success": true,
  "data": [
    {
      "id": 42,
      "name": "Electrical · WMT-2405",
      "trade": "Electrical",
      "status": "open",
      "due_date": "2026-06-15",
      "bids_received": 2,
      "invitations_sent": 8
    }
  ]
}
```

---

### POST `/projects/{id}/packages`
Create a new bid package.

**Auth Required:** Yes | **Role:** estimator, sr_estimator

**Request Body:**
```json
{
  "name": "Electrical · WMT-2405",
  "trade": "Electrical",
  "scope_id": 20,
  "due_date": "2026-06-15",
  "drawing_set": [10, 11, 12]
}
```

**Response `201`:**
```json
{
  "success": true,
  "data": {
    "id": 42,
    "name": "Electrical · WMT-2405",
    "status": "draft",
    "public_token": "tok_abc123",
    "inbound_email_alias": "pkg-cedar-elec@inbound.bidflow.io"
  },
  "message": "Bid package created"
}
```

---

### GET `/packages/{id}`
Get bid package details.

**Auth Required:** Yes

**Response `200`:**
```json
{
  "success": true,
  "data": {
    "id": 42,
    "name": "Electrical · WMT-2405",
    "trade": "Electrical",
    "status": "open",
    "due_date": "2026-06-15",
    "drawing_set": [10, 11, 12],
    "scope": { "id": 20, "title": "Electrical Scope" },
    "inbound_email_alias": "pkg-cedar-elec@inbound.bidflow.io",
    "public_token": "tok_abc123"
  }
}
```

---

### PATCH `/packages/{id}`
Update a bid package.

**Auth Required:** Yes | **Role:** estimator, sr_estimator

**Request Body:**
```json
{
  "due_date": "2026-06-20",
  "drawing_set": [10, 11, 12, 13]
}
```

**Response `200`:**
```json
{
  "success": true,
  "data": { "id": 42, "due_date": "2026-06-20" },
  "message": "Package updated"
}
```

---

### DELETE `/packages/{id}`
Delete a bid package (only when status is draft).

**Auth Required:** Yes | **Role:** admin, sr_estimator

**Response `200`:**
```json
{
  "success": true,
  "message": "Package deleted"
}
```

---

### POST `/packages/{id}/send`
Send invitations and change package status from draft → sent.

**Auth Required:** Yes | **Role:** estimator, sr_estimator

**Response `200`:**
```json
{
  "success": true,
  "data": { "id": 42, "status": "sent", "invitations_queued": 8 },
  "message": "Invitations sent"
}
```

---

### POST `/packages/{id}/close`
Close bidding on a package (open → closed).

**Auth Required:** Yes | **Role:** sr_estimator, admin

**Response `200`:**
```json
{
  "success": true,
  "data": { "id": 42, "status": "closed" },
  "message": "Package closed"
}
```

---

### GET `/packages/{id}/coverage`
Get trade coverage summary (invited/opened/bidding/received/declined).

**Auth Required:** Yes

**Response `200`:**
```json
{
  "success": true,
  "data": {
    "package_id": 42,
    "invited": 8,
    "opened": 5,
    "bidding": 3,
    "bid_received": 2,
    "declined": 1,
    "no_response": 2,
    "coverage_alert": false
  }
}
```

---

## 8. Subcontractors (CRM)

### GET `/subs`
List all subcontractors in the org.

**Auth Required:** Yes

**Query Parameters:**

| Parameter | Type   | Required | Description               |
|-----------|--------|----------|---------------------------|
| `trades`  | string | No       | Comma-separated trades    |
| `regions` | string | No       | Comma-separated regions   |
| `search`  | string | No       | Search by company name    |
| `page`    | integer| No       | Page number               |

**Response `200`:**
```json
{
  "success": true,
  "data": [
    {
      "id": 100,
      "company_name": "Apex Electric Co.",
      "trades": ["Electrical"],
      "regions": ["LA", "OC"],
      "union_status": "union",
      "internal_rating": 4,
      "do_not_invite": false
    }
  ]
}
```

---

### POST `/subs`
Create a new subcontractor.

**Auth Required:** Yes | **Role:** estimator, coordinator, sr_estimator, admin

**Request Body:**
```json
{
  "company_name": "Apex Electric Co.",
  "trades": ["Electrical"],
  "regions": ["LA", "OC"],
  "union_status": "union",
  "bonding_capacity": 5000000,
  "internal_rating": 4,
  "tags": ["preferred", "fast-response"]
}
```

**Response `201`:**
```json
{
  "success": true,
  "data": { "id": 100, "company_name": "Apex Electric Co." },
  "message": "Subcontractor created"
}
```

---

### GET `/subs/{id}`
Get subcontractor details with contacts and activity.

**Auth Required:** Yes

**Response `200`:**
```json
{
  "success": true,
  "data": {
    "id": 100,
    "company_name": "Apex Electric Co.",
    "trades": ["Electrical"],
    "regions": ["LA", "OC"],
    "union_status": "union",
    "bonding_capacity": 5000000,
    "internal_rating": 4,
    "do_not_invite": false,
    "tags": ["preferred"],
    "contacts": [],
    "bids_submitted": 12,
    "awards_won": 3
  }
}
```

---

### PATCH `/subs/{id}`
Update a subcontractor record.

**Auth Required:** Yes | **Role:** estimator, coordinator, sr_estimator, admin

**Request Body:**
```json
{
  "internal_rating": 5,
  "do_not_invite": false,
  "tags": ["preferred", "certified"]
}
```

**Response `200`:**
```json
{
  "success": true,
  "data": { "id": 100, "internal_rating": 5 },
  "message": "Subcontractor updated"
}
```

---

### DELETE `/subs/{id}`
Delete a subcontractor.

**Auth Required:** Yes | **Role:** admin

**Response `200`:**
```json
{
  "success": true,
  "message": "Subcontractor deleted"
}
```

---

### POST `/subs/import`
Bulk import subcontractors from a CSV file.

**Auth Required:** Yes | **Role:** admin, sr_estimator

**Request Body:** `multipart/form-data`

| Field  | Type | Required | Description        |
|--------|------|----------|--------------------|
| `file` | file | Yes      | CSV file           |

**Response `202`:**
```json
{
  "success": true,
  "data": {
    "job_id": "import_xyz",
    "rows_detected": 150
  },
  "message": "Import queued"
}
```

---

### GET `/subs/{id}/activity`
Get activity log for a subcontractor.

**Auth Required:** Yes

**Response `200`:**
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "type": "invitation_sent",
      "description": "Invited to Electrical · WMT-2405",
      "actor": "John Doe",
      "created_at": "2026-05-01T10:00:00Z"
    }
  ]
}
```

---

### POST `/packages/{id}/suggest-subs`
AI-suggest best-fit subcontractors for a bid package.

**Auth Required:** Yes | **Role:** estimator, sr_estimator

**Response `200`:**
```json
{
  "success": true,
  "data": [
    {
      "sub_id": 100,
      "company_name": "Apex Electric Co.",
      "match_score": 0.94,
      "reasons": ["Past bids on similar projects", "High rating", "Local region"]
    }
  ]
}
```

---

## 9. Contacts

### GET `/subs/{id}/contacts`
List all contacts for a subcontractor.

**Auth Required:** Yes

**Response `200`:**
```json
{
  "success": true,
  "data": [
    {
      "id": 50,
      "name": "Mike Johnson",
      "title": "Estimator",
      "email": "mike@apexelectric.com",
      "phone": "310-555-1234",
      "is_primary": true
    }
  ]
}
```

---

### POST `/subs/{id}/contacts`
Add a contact to a subcontractor.

**Auth Required:** Yes | **Role:** estimator, coordinator, sr_estimator, admin

**Request Body:**
```json
{
  "name": "Mike Johnson",
  "title": "Estimator",
  "email": "mike@apexelectric.com",
  "phone": "310-555-1234",
  "is_primary": true
}
```

**Response `201`:**
```json
{
  "success": true,
  "data": { "id": 50, "name": "Mike Johnson", "email": "mike@apexelectric.com" },
  "message": "Contact added"
}
```

---

### PATCH `/contacts/{id}`
Update a contact.

**Auth Required:** Yes | **Role:** estimator, coordinator, sr_estimator, admin

**Request Body:**
```json
{
  "phone": "310-555-9999",
  "is_primary": false
}
```

**Response `200`:**
```json
{
  "success": true,
  "data": { "id": 50, "phone": "310-555-9999" },
  "message": "Contact updated"
}
```

---

### DELETE `/contacts/{id}`
Delete a contact.

**Auth Required:** Yes | **Role:** admin, sr_estimator

**Response `200`:**
```json
{
  "success": true,
  "message": "Contact deleted"
}
```

---

## 10. Invitations

### GET `/packages/{id}/invitations`
List all invitations for a bid package.

**Auth Required:** Yes

**Query Parameters:**

| Parameter | Type   | Required | Description                                         |
|-----------|--------|----------|-----------------------------------------------------|
| `status`  | string | No       | queued, sent, opened, viewed, bidding, bid_received |

**Response `200`:**
```json
{
  "success": true,
  "data": [
    {
      "id": 200,
      "sub": { "id": 100, "company_name": "Apex Electric Co." },
      "status": "bid_received",
      "sent_at": "2026-05-01T09:00:00Z",
      "opened_at": "2026-05-01T11:00:00Z",
      "bid_received_at": "2026-05-10T14:00:00Z",
      "match_score": 0.94
    }
  ]
}
```

---

### POST `/packages/{id}/invitations`
Create invitations for selected subcontractors.

**Auth Required:** Yes | **Role:** estimator, sr_estimator

**Request Body:**
```json
{
  "sub_ids": [100, 101, 102],
  "contact_ids": [50, 51]
}
```

**Response `201`:**
```json
{
  "success": true,
  "data": { "invitations_created": 3 },
  "message": "Invitations created"
}
```

---

### GET `/invitations/{id}`
Get a single invitation's details.

**Auth Required:** Yes

**Response `200`:**
```json
{
  "success": true,
  "data": {
    "id": 200,
    "package_id": 42,
    "sub_id": 100,
    "status": "bid_received",
    "access_token": "acc_tok_xyz",
    "sent_at": "2026-05-01T09:00:00Z",
    "opened_at": "2026-05-01T11:00:00Z"
  }
}
```

---

### DELETE `/invitations/{id}`
Revoke an invitation.

**Auth Required:** Yes | **Role:** estimator, sr_estimator, admin

**Response `200`:**
```json
{
  "success": true,
  "message": "Invitation revoked"
}
```

---

### POST `/invitations/{id}/resend`
Resend an invitation email.

**Auth Required:** Yes | **Role:** estimator, coordinator, sr_estimator

**Response `200`:**
```json
{
  "success": true,
  "message": "Invitation resent",
  "data": { "resent_at": "2026-05-13T10:00:00Z" }
}
```

---

## 11. Sub Planroom (No-Login)

> These endpoints use **token-based auth** via URL parameter `?t={access_token}`. No Bearer token required.

### GET `/p/{token}`
Get planroom data for a subcontractor (plans, scope, package info).

**Auth Required:** Token in URL (`?t=access_token`)

**Response `200`:**
```json
{
  "success": true,
  "data": {
    "package": {
      "id": 42,
      "name": "Electrical · WMT-2405",
      "due_date": "2026-06-15",
      "trade": "Electrical"
    },
    "sheets": [],
    "scope": { "title": "Electrical Scope", "description": "..." },
    "bid_submitted": false
  }
}
```

---

### POST `/p/{token}/bid`
Submit a bid from the planroom.

**Auth Required:** Token in URL

**Request Body:**
```json
{
  "amount": 850000,
  "exclusions": "Temporary power not included",
  "inclusions": "All labor and materials",
  "notes": "Lead time on switchgear is 16 weeks",
  "file_ids": ["file_abc"]
}
```

**Response `201`:**
```json
{
  "success": true,
  "data": { "bid_id": 300, "amount": 850000 },
  "message": "Bid submitted successfully"
}
```

---

### POST `/p/{token}/decline`
Decline an invitation from the planroom.

**Auth Required:** Token in URL

**Request Body:**
```json
{
  "reason": "Fully booked for the bid period"
}
```

**Response `200`:**
```json
{
  "success": true,
  "message": "Invitation declined"
}
```

---

### POST `/p/{token}/rfi`
Submit a Request for Information (RFI) from the planroom.

**Auth Required:** Token in URL

**Request Body:**
```json
{
  "question": "What is the panel schedule for the main electrical room?",
  "sheet_reference": "E-101"
}
```

**Response `201`:**
```json
{
  "success": true,
  "data": { "rfi_id": 400 },
  "message": "RFI submitted"
}
```

---

## 12. Bids

### GET `/packages/{id}/bids`
List all bids received for a bid package.

**Auth Required:** Yes | **Role:** estimator, sr_estimator, admin

**Response `200`:**
```json
{
  "success": true,
  "data": [
    {
      "id": 300,
      "sub": { "id": 100, "company_name": "Apex Electric Co." },
      "amount": 850000,
      "exclusions": "Temporary power",
      "inclusions": "All labor and materials",
      "submitted_at": "2026-05-10T14:00:00Z"
    }
  ]
}
```

---

### GET `/bids/{id}`
Get a single bid's full detail.

**Auth Required:** Yes

**Response `200`:**
```json
{
  "success": true,
  "data": {
    "id": 300,
    "package_id": 42,
    "invitation_id": 200,
    "sub_id": 100,
    "amount": 850000,
    "exclusions": "Temporary power not included",
    "inclusions": "All labor and materials",
    "notes": "Lead time on switchgear is 16 weeks",
    "file_urls": [],
    "submitted_at": "2026-05-10T14:00:00Z"
  }
}
```

---

### PATCH `/bids/{id}`
Update bid details (admin/estimator only — for corrections).

**Auth Required:** Yes | **Role:** sr_estimator, admin

**Request Body:**
```json
{
  "amount": 860000,
  "notes": "Revised after clarification"
}
```

**Response `200`:**
```json
{
  "success": true,
  "data": { "id": 300, "amount": 860000 },
  "message": "Bid updated"
}
```

---

## 13. Bid Leveling & Award

### GET `/packages/{id}/leveling`
Get the bid leveling comparison table for a package.

**Auth Required:** Yes | **Role:** sr_estimator, estimator, admin

**Response `200`:**
```json
{
  "success": true,
  "data": {
    "package_id": 42,
    "bids": [
      {
        "bid_id": 300,
        "sub": { "id": 100, "company_name": "Apex Electric Co." },
        "amount": 850000,
        "normalized_amount": 855000,
        "exclusions": "Temporary power",
        "notes": "",
        "rank": 1
      }
    ]
  }
}
```

---

### PATCH `/packages/{id}/leveling`
Save leveling notes and normalization adjustments.

**Auth Required:** Yes | **Role:** sr_estimator, admin

**Request Body:**
```json
{
  "leveling_data": [
    {
      "bid_id": 300,
      "normalized_amount": 855000,
      "notes": "Added $5k for temp power clarification"
    }
  ]
}
```

**Response `200`:**
```json
{
  "success": true,
  "message": "Leveling saved"
}
```

---

### POST `/packages/{id}/award`
Award the bid package to the winning subcontractor.

**Auth Required:** Yes | **Role:** sr_estimator, admin

**Request Body:**
```json
{
  "bid_id": 300,
  "award_amount": 850000,
  "notes": "Best value, strong track record"
}
```

**Response `201`:**
```json
{
  "success": true,
  "data": {
    "award_id": 700,
    "bid_id": 300,
    "sub": { "id": 100, "company_name": "Apex Electric Co." },
    "amount": 850000
  },
  "message": "Award created"
}
```

---

### GET `/awards/{id}`
Get award detail.

**Auth Required:** Yes

**Response `200`:**
```json
{
  "success": true,
  "data": {
    "id": 700,
    "package_id": 42,
    "bid_id": 300,
    "sub_id": 100,
    "amount": 850000,
    "contract_url": null,
    "pushed_to_procore": false,
    "created_at": "2026-05-13T10:00:00Z"
  }
}
```

---

### POST `/awards/{id}/push-to-procore`
Push the awarded commitment to Procore.

**Auth Required:** Yes | **Role:** sr_estimator, admin

**Response `200`:**
```json
{
  "success": true,
  "data": { "procore_commitment_id": "PC-9981" },
  "message": "Pushed to Procore successfully"
}
```

---

### POST `/awards/{id}/send-contract`
Send contract to sub via DocuSign for signature.

**Auth Required:** Yes | **Role:** sr_estimator, admin

**Request Body:**
```json
{
  "contract_template_id": "tmpl_docusign_abc",
  "signer_email": "mike@apexelectric.com",
  "signer_name": "Mike Johnson"
}
```

**Response `200`:**
```json
{
  "success": true,
  "data": {
    "docusign_envelope_id": "env_xyz123",
    "status": "sent"
  },
  "message": "Contract sent via DocuSign"
}
```

---

## HTTP Status Codes

| Code | Meaning                        |
|------|--------------------------------|
| 200  | OK — Request successful        |
| 201  | Created — Resource created     |
| 202  | Accepted — Job queued          |
| 400  | Bad Request — Validation error |
| 401  | Unauthorized — Auth required   |
| 403  | Forbidden — Insufficient role  |
| 404  | Not Found                      |
| 409  | Conflict — Duplicate / state   |
| 422  | Unprocessable Entity           |
| 500  | Internal Server Error          |

---

## Roles & Permissions

| Role           | Capabilities                                               |
|----------------|------------------------------------------------------------|
| `admin`        | Full access — org settings, users, integrations, all data |
| `sr_estimator` | Approve scopes, level bids, award packages                 |
| `estimator`    | Create packages, manage subs, track coverage               |
| `coordinator`  | Manage invite lists, chase replies, update sub records     |
| `viewer`       | Read-only access to all data                               |
| `sub`          | External — token-auth planroom only                        |

---

## Realtime (WebSocket)

**Endpoint:** `wss://yourdomain.com/ws`

**Topics:**
- `project:{id}` — Project-level events
- `package:{id}` — Package/bid events
- `user:{id}` — Personal notifications

---

*Generated for Bidflow v1 — Last updated: 2026-05-13*
