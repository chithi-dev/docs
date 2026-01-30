---
icon: lucide/house
---

Chithi is based on client-server architecture.

# Overall Architecture

The architecture is based on a [zero-trust backend server](https://www.cloudflare.com/learning/security/glossary/what-is-zero-trust/).

```mermaid
flowchart TD
    UserA[Sender Browser]
    UserB[Receiver Browser]

    subgraph Client_Side["Client Side (Browser)"]
        Encrypt[Encrypt File<br/>AES-GCM-256]
        KeyGen[Generate Random Key]
        Hash[Derive File Hash / Metadata]
        Decrypt[Decrypt File]
    end

    subgraph Server_Side["Firefox Send Server (Untrusted)"]
        UploadAPI[Upload API]
        DownloadAPI[Download API]
        Storage[(Encrypted File Storage)]
        MetadataDB[(Metadata DB)]
        Expiry[Expiry / Download Counter]
    end

    %% Sender Flow
    UserA --> KeyGen
    KeyGen --> Encrypt
    UserA --> Encrypt
    Encrypt --> UploadAPI
    Hash --> UploadAPI

    UploadAPI --> Storage
    UploadAPI --> MetadataDB

    %% Link Creation
    Encrypt -->|Key stays in URL fragment| Link[Shareable Link]
    Link --> UserB

    %% Receiver Flow
    UserB --> DownloadAPI
    DownloadAPI --> Storage
    DownloadAPI --> MetadataDB
    Storage --> UserB
    MetadataDB --> Expiry

    %% Decryption
    UserB --> Decrypt
    Decrypt -->|Uses key from URL fragment| UserB

    %% Security Boundary
    Server_Side -. Never sees encryption key .- Client_Side

```

# Backend

The backend handles file deletion and file storage:

```mermaid
flowchart TD
    Client[Client Browser]

    subgraph API["Backend API"]
        UploadAPI[Upload API]
        DownloadAPI[Download API]
    end

    subgraph DB["PostgreSQL"]
        Meta[(File Metadata)]
    end

    subgraph Queue["Task Queue"]
        Redis[(Redis)]
    end

    subgraph Workers["Async Workers"]
        Celery[Celery Worker]
        Beat[Celery Beat]
    end

    subgraph Storage["Blob Storage"]
        Files[(Encrypted Files)]
    end

    %% Upload Flow
    Client --> UploadAPI
    UploadAPI --> Files
    UploadAPI --> Meta

    %% Download Flow
    Client --> DownloadAPI
    DownloadAPI --> Files
    DownloadAPI --> Meta

    %% Cleanup Trigger
    DownloadAPI -->|remaining_downloads = 0| Redis

    %% Periodic Cleanup
    Beat -->|find expired| Meta
    Beat --> Redis

    %% Cleanup Execution
    Redis --> Celery
    Celery --> Files
    Celery --> Meta

    %% Status Update
    Meta -->|mark deleted| Meta

```

# Frontend

The frontend does the actual encryption/decrpytion:

```mermaid
flowchart TD
    User[User]
    UI[Svelte UI]

    subgraph Client["Browser - Svelte App"]
        Select["Select Files"]
        Zip["ZIP Files"]
        KeyGen["Generate Random AES-256 Key"]
        IKMGen["Generate Random IKM"]
        SN1["Build SN1 Header<br/>IKM and Metadata"]
        Encrypt["Encrypt ZIP<br/>AES-256-GCM"]
        Upload["Upload Encrypted Blob"]
        Link["Generate Share Link<br/>Key in URL Fragment"]

        Fetch["Fetch Encrypted Blob"]
        ParseSN1["Parse SN1 Header"]
        AskPass["Prompt for Password"]
        Derive["Derive Wrapping Key"]
        UnlockIKM["Decrypt IKM"]
        Decrypt["Decrypt ZIP"]
        Unzip["Unzip Files"]
        Download["Download to User"]
        Error["Show Decryption Error"]
    end

    Backend[(Backend Storage)]

    %% Upload Flow
    User --> UI --> Select
    Select --> Zip
    Zip --> KeyGen
    KeyGen --> Encrypt
    IKMGen --> SN1
    SN1 --> Encrypt
    Encrypt --> Upload
    Upload --> Backend
    Encrypt --> Link
    Link --> User

    %% Download Flow
    User --> UI --> Fetch
    Fetch --> Backend
    Backend --> Fetch

    Fetch --> ParseSN1
    ParseSN1 -->|Password Protected| AskPass
    AskPass --> Derive
    Derive --> UnlockIKM
    UnlockIKM --> Decrypt

    ParseSN1 -->|No Password| Decrypt
    Decrypt -->|Auth OK| Unzip
    Decrypt -->|Auth Fail| Error

    Unzip --> Download
    Download --> User

```
