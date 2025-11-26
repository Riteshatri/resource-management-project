# Use official Azure SQL Server image
FROM mcr.microsoft.com/mssql/server:2022-latest AS azure-sql

# Use SQLite for development alternative
FROM python:3.11-slim AS sqlite-dev

WORKDIR /app

# Copy database scripts
COPY . .

# Install SQLite utilities
RUN apt-get update && apt-get install -y --no-install-recommends \
    sqlite3 \
    && rm -rf /var/lib/apt/lists/*

# Default to sqlite-dev for development
FROM sqlite-dev

ENV DATABASE_TYPE=sqlite \
    DATABASE_PATH=/app/data

# Create data directory
RUN mkdir -p /app/data

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD sqlite3 /app/data/app.db "SELECT 1;" || exit 1

# Default command - just keep container running for SQL scripts
CMD ["tail", "-f", "/dev/null"]

# NOTE: For production Azure SQL:
# - Set DATABASE_TYPE=azure_sql
# - Configure via environment variables:
#   - AZURE_SQL_SERVER
#   - AZURE_SQL_DATABASE
#   - AZURE_SQL_USERNAME
#   - AZURE_SQL_PASSWORD
