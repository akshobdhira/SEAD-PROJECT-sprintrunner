# Team Workspace Setup
## Sprint Retrospective Analyzer - CASE Tool Configuration

**Document Version:** 1.0  
**Date:** January 27, 2026  
**Problem Statement:** 21 - Team Workspace Setup

---

## 1. Introduction

This document describes the team workspace setup for the Sprint Retrospective Analyzer project using Computer-Aided Software Engineering (CASE) tools. A properly configured workspace ensures collaborative design, version control, and standardized UML diagram creation.

---

## 2. Selected CASE Tool

**Primary Tool:** Visual Paradigm Community Edition  
**License:** Free for academic use  
**Download:** https://www.visual-paradigm.com/download/community.jsp

**Rationale for Selection:**
- ✅ Comprehensive UML 2.0 support (use case, class, sequence, activity, component, deployment)
- ✅ Team collaboration features (cloud workspace, version control)
- ✅ Export capabilities (PNG, PDF, SVG)
- ✅ Free for educational projects
- ✅ Cross-platform (Windows, macOS, Linux)

---

## 3. Workspace Configuration

### 3.1 Workspace Details

| **Attribute** | **Value** |
|---------------|-----------|
| **Workspace Name** | Sprint Retrospective Analyzer Design |
| **Location** | `C:\Projects\SRA-Design` (Local) + Cloud Sync |
| **Team Size** | 6 members (1 Lead, 1 Architect, 3 Developers, 1 QA) |
| **Access Control** | Role-based (Admin, Editor, Viewer) |
| **Version Control** | Git integration enabled |

### 3.2 Team Members and Roles

| **Name** | **Role** | **Access Level** | **Responsibilities** |
|----------|----------|------------------|----------------------|
| Project Lead | Admin | Full access | Workspace management, final approvals |
| System Architect | Editor | Create/Edit diagrams | Architecture design, class diagrams, sequence diagrams |
| Developer 1 | Editor | Create/Edit assigned diagrams | Use case diagrams, activity diagrams |
| Developer 2 | Editor | Create/Edit assigned diagrams | Sequence diagrams, component diagrams |
| Developer 3 | Editor | Create/Edit assigned diagrams | Deployment diagrams, database design |
| QA Team Member | Viewer | Read-only, comments | Review diagrams, provide feedback |

---

## 4. Workspace Structure

### 4.1 Project Hierarchy

Created in Visual Paradigm, the following folder structure organizes all design artifacts:

```
© Sprint Retrospective Analyzer
  │
  ├── 📁 01_Requirements
  │     ├── Use_Cases_Model
  │     └── User_Stories
  │
  ├── 📁 02_Analysis
  │     ├── Domain_Class_Model
  │     └── Conceptual_Model
  │
  ├── 📁 03_Design
  │     ├── 📁 Class_Diagrams
  │     │     ├── User_Management_Classes
  │     │     ├── Retrospective_Classes
  │     │     ├── Analytics_Classes
  │     │     └── Utility_Classes
  │     │
  │     ├── 📁 Sequence_Diagrams
  │     │     ├── Submit_Feedback_Sequence
  │     │     ├── Close_Retrospective_Sequence
  │     │     ├── Pattern_Analysis_Sequence
  │     │     └── Generate_Report_Sequence
  │     │
  │     ├── 📁 Activity_Diagrams
  │     │     ├── Retrospective_Workflow
  │     │     └── Action_Item_Lifecycle
  │     │
  │     ├── 📁 Component_Diagram
  │     │     └── System_Architecture
  │     │
  │     └── 📁 Deployment_Diagram
  │           └── Cloud_Deployment
  │
  └── 📁 04_Database
        └── ERD_Diagram
```

---

## 5. Installation and Setup Steps

### 5.1 Visual Paradigm Installation (Windows)

**Step 1: Download**
1. Navigate to https://www.visual-paradigm.com/download/community.jsp
2. Select **Visual Paradigm Community Edition** for Windows 64-bit
3. Download installer (approximately 300 MB)

**Step 2: Install**
1. Run `Visual_Paradigm_CE_Win64_17_1.exe`
2. Follow installation wizard:
   - Accept license agreement
   - Choose installation directory: `C:\Program Files\Visual Paradigm CE`
   - Select all components (recommended)
   - Create desktop shortcut
3. Click **Install** and wait for completion (5-10 minutes)

**Step 3: Launch**
1. Double-click Visual Paradigm CE icon
2. For Community Edition, no license key needed
3. Skip registration (optional for community version)

**Step 4: Initial Configuration**
1. Go to **Tools > Options**
2. Configure:
   - **General > Auto-save:** Every 5 minutes
   - **Diagram > Default Font:** Arial, size 11
   - **Diagram > Color Scheme:** Light (for better printing)
   - **Version Control:** Enable Git integration

---

### 5.2 Creating Team Workspace

**Option 1: Visual Paradigm Cloud (Recommended for Teams)**

1. **Create Cloud Workspace**
   - Click **Team > Teamwork Server**
   - Sign up at https://cloud.visual-paradigm.com
   - Create new workspace: "SRA-Design-2026"
   - Select plan: Free (up to 3 users) or Team plan

2. **Invite Team Members**
   - Click **Manage Team Members**
   - Add team member emails
   - Assign roles:
     - Admin: Project Lead
     - Editor: Architects, Developers
     - Viewer: QA, Stakeholders

3. **Enable Version Control**
   - **Team > Version Control > Enable VCS**
   - All changes automatically versioned
   - View commit history: **Team > Version History**

**Option 2: Git Integration (For Advanced Teams)**

1. **Initialize Git Repository**
   ```powershell
   cd C:\Projects\SRA-Design
   git init
   git remote add origin https://github.com/your-org/sra-design.git
   ```

2. **Configure VP for Git**
   - Go to **Tools > Version Control > Git Options**
   - Set repository path: `C:\Projects\SRA-Design`
   - Enable auto-commit on save (optional)

3. **Create `.gitignore`**
   ```
   *.lock
   *.tmp
   *.bak
   ~$*.vpp
   .vpp~*
   ```

---

## 6. Workspace Standards

### 6.1 Naming Conventions

| **Element** | **Convention** | **Example** |
|-------------|----------------|-------------|
| **Diagrams** | `[Type]_[Subject]_v[Version]` | `UseCase_RetrospectiveManagement_v1.0` |
| **Actors** | Title Case | Team Member, Scrum Master |
| **Use Cases** | Verb + Noun | Create Retrospective, Submit Feedback |
| **Classes** | PascalCase | User, Retrospective, ActionItem |
| **Methods** | camelCase | createRetrospective(), submitFeedback() |
| **Attributes** | camelCase | userId, retrospectiveId, createdAt |

### 6.2 Color Coding Standards

| **Element** | **Color** | **Hex Code** | **Usage** |
|-------------|-----------|--------------|-----------|
| **Actors** | Light Blue | #A3C1DA | Consistent across all use case diagrams |
| **Use Cases** | Light Gray | #E0E0E0 | Standard use cases |
| **Classes (Entity)** | Light Green | #C8E6C9 | Domain model classes |
| **Classes (Control)** | Light Orange | #FFE0B2 | Controller/service classes |
| **Classes (Boundary)** | Light Purple | #E1BEE7 | UI/API interface classes |
| **Database** | Light Yellow | #FFF9C4 | Database components |

---

## 7. Collaboration Workflow

### 7.1 Daily Workflow

**Morning:**
1. Open Visual Paradigm
2. **Team > Sync with Server** (pull latest changes)
3. Review notifications for diagram updates

**During Work:**
1. Create/edit assigned diagrams
2. Follow naming conventions and color standards
3. Add descriptive comments for complex relationships
4. Auto-save enabled (every 5 minutes)

**End of Day:**
1. **File > Save All**
2. **Team > Commit Changes** with descriptive message
   - Example: "Added sequence diagram for pattern analysis"
3. **Team > Push to Server**

### 7.2 Review Process

**Weekly Diagram Review (Every Friday):**
1. Team Lead selects diagrams for review
2. Share diagram links via email/Slack
3. Team members add comments directly in VP
4. Address comments before next sprint
5. Mark diagrams as "Approved" when finalized

---

## 8. Backup and Recovery

### 8.1 Automatic Backups

**Configuration:**
1. Go to **Tools > Options > General**
2. Enable **Automatic Backup**
3. Settings:
   - Backup frequency: Every 30 minutes
   - Backup location: `C:\Projects\SRA-Design\Backups`
   - Keep last: 10 backup versions

### 8.2 Manual Backups

**Weekly Manual Backup (Recommended):**
1. **File > Export > Export Project**
2. Export to: `C:\Projects\SRA-Design\Backups\SRA_Backup_YYYYMMDD.vpp`
3. Upload to cloud storage (Google Drive, OneDrive)

---

## 9. Troubleshooting

### Common Issues

**Issue 1: "Cannot find Java Runtime"**
- **Solution:** Install Java JRE 8+ from https://www.java.com/download/
- Set `JAVA_HOME` environment variable

**Issue 2: "Sync failed - Connection timeout"**
- **Solution:** Check internet connection, verify firewall allows VP to connect
- Alternative: Use Git integration instead of cloud sync

**Issue 3: "Out of memory error"**
- **Solution:** Increase memory allocation
  1. Edit `vp.ini` in installation directory
  2. Change `-Xmx1024m` to `-Xmx2048m`
  3. Restart Visual Paradigm

---

## 10. Success Criteria

Workspace setup is complete when:

- ✅ All 6 team members have VP installed and configured
- ✅ Team workspace created with proper role assignments
- ✅ Folder structure created as per Section 4.1
- ✅ Naming conventions and color standards documented and shared
- ✅ Version control (cloud or Git) enabled and tested
- ✅ Automatic backups configured
- ✅ All team members can create, edit, and commit diagrams

---

## 11. Next Steps

After workspace setup:
1. Proceed to **[22_Project_Structure_Creation.md](file:///C:/Users/AKSHOB%20DHIRA/SEAD-PROJECT-sprintrunner/22_Project_Structure_Creation.md)** to populate project models
2. Conduct team training session (2 hours) on VP basics
3. Review and approve workspace setup with Project Lead

---

**Document Control:**
- **Created By:** System Design Team
- **Last Updated:** January 27, 2026
- **Status:** Final
- **Version:** 1.0
- **Next Review:** February 27, 2026
