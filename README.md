# Home-Cloud-Storage

Stack for Azurite Local Blob, Table & Queue Storage with Traefik reverse proxy integration.

## Overview

This project provides a Docker Compose setup for running [Azurite](https://github.com/Azure/Azurite), Microsoft's official Azure Storage emulator. It enables local development and testing of Azure Storage services (Blob, Queue, and Table) with Traefik reverse proxy for domain-based routing.

## Features

- **Blob Storage** - Compatible with Azure Blob Storage APIs
- **Queue Storage** - Compatible with Azure Queue Storage APIs
- **Table Storage** - Compatible with Azure Table Storage APIs
- **Traefik Integration** - Domain-based routing with SSL/TLS support
- **Persistent Storage** - Data persisted to host filesystem
- **Debug Logging** - Built-in debug logging for troubleshooting

## Prerequisites

- Docker and Docker Compose
- Traefik reverse proxy configured and running
- External Docker network for Traefik integration

## Configuration

### Environment Variables

Copy `.env.example` to `.env` and configure the following variables:

#### Domain Configuration

```bash
DOMAIN=home-cloud\\.uk
```

Your domain for Traefik routing. Dots must be escaped with `\\` for regex matching.

Examples:

- `home-cloud\\.uk`
- `home-cloud\\.com`
- `home-cloud\\.co\\.uk`

#### Network Configuration

```bash
STORAGE_NETWORK=home_cloud_storage_default
```

The Docker network name that Azurite and Traefik will use. This network must exist before starting the stack.

#### Volume Configuration

```bash
AZURITE_DATA_PATH=/mnt/storage/azurerite
```

Path on the host where Azurite data will be stored. Ensure this directory exists and has appropriate permissions.

#### Storage Accounts Configuration

```bash
AZURITE_ACCOUNTS=devstoreaccount1:Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

Storage account credentials in the format: `account1:key1[:account2:key2:...]`

The example above uses the default Azure Storage Emulator account credentials.

## Azurite Configuration

The stack runs Azurite with the following configuration:

- **Blob Service Port**: 10000
- **Queue Service Port**: 10001
- **Table Service Port**: 10002
- **Data Location**: `/data` (mounted from `AZURITE_DATA_PATH`)
- **Debug Log**: `/data/debug.log`
- **Loose Mode**: Enabled (less strict validation)
- **Skip API Version Check**: Enabled

### Service Endpoints

When running with Traefik, services are accessible via:

- **Blob Storage**: `https://<account>.blob.<DOMAIN>`
- **Queue Storage**: `https://<account>.queue.<DOMAIN>`
- **Table Storage**: `https://<account>.table.<DOMAIN>`

Replace `<account>` with your storage account name and `<DOMAIN>` with your configured domain.

## Setup

1. Clone this repository
2. Copy the environment file:
   ```bash
   cp .env.example .env
   ```
3. Edit `.env` with your configuration
4. Create the external network if it doesn't exist:
   ```bash
   docker network create <STORAGE_NETWORK>
   ```
5. Create the data directory:
   ```bash
   mkdir -p <AZURITE_DATA_PATH>
   ```
6. Start the stack:
   ```bash
   docker-compose -f DockerCompose.yaml up -d
   ```

## Usage

### Connecting to Azurite

Use the standard Azure Storage SDK with your configured account name and key. Point your connection string to the appropriate endpoint:

```
DefaultEndpointsProtocol=https;
AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=https://devstoreaccount1.blob.<DOMAIN>;
QueueEndpoint=https://devstoreaccount1.queue.<DOMAIN>;
TableEndpoint=https://devstoreaccount1.table.<DOMAIN>;
```

### Viewing Logs

```bash
docker-compose -f DockerCompose.yaml logs -f azurite
```

Debug logs are also available at `<AZURITE_DATA_PATH>/debug.log`

## Advanced Configuration

For detailed Azurite configuration options, command-line parameters, and advanced features, see the official documentation:

**[Azurite GitHub Repository](https://github.com/Azure/Azurite)**

## License

This project configuration is provided as-is. Azurite is licensed under the MIT License.
