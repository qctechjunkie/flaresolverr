# FlareSolverr Docker Stack

CloudFlare bypass service that acts as a proxy server to bypass CloudFlare protection for web scraping applications.

## Quick Start

```bash
cp .env.example .env
# Edit .env if needed
docker-compose up -d
```

FlareSolverr will be available at: http://localhost:8191

## What is FlareSolverr?

FlareSolverr is a proxy server that solves CloudFlare challenges automatically. It's commonly used with:
- **Prowlarr** - For accessing CloudFlare-protected indexers
- **Jackett** - For torrent/usenet indexer access
- **Sonarr/Radarr/Lidarr** - Indirectly through Prowlarr

## Configuration

### Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `FLARESOLVERR_PORT` | 8191 | Service port |
| `LOG_LEVEL` | info | Logging level (verbose, info, warning, error) |
| `LOG_HTML` | false | Log HTML responses for debugging |
| `CAPTCHA_SOLVER` | none | CAPTCHA solving service |
| `TZ` | America/New_York | Timezone |

### CAPTCHA Solvers

FlareSolverr supports these CAPTCHA solving services:
- `none` - No CAPTCHA solving (default)
- `anticaptcha` - AntiCaptcha service
- `2captcha` - 2captcha service  
- `deathbycaptcha` - DeathByCaptcha service

## Usage

### API Endpoint
- **URL**: `http://localhost:8191/v1`
- **Method**: POST
- **Content-Type**: application/json

### Example Request
```json
{
    "cmd": "request.get",
    "url": "https://example.com",
    "userAgent": "Mozilla/5.0...",
    "maxTimeout": 60000
}
```

## Integration with *arr Services

### Prowlarr Integration
1. Go to Prowlarr → Settings → Indexers
2. Add FlareSolverr:
   - **Name**: FlareSolverr
   - **Tags**: flaresolverr (or custom tag)
   - **Host**: `http://flaresolverr:8191/` (if using Docker networks)
   - **Host**: `http://localhost:8191/` (if not using Docker networks)

3. Configure indexers to use FlareSolverr:
   - Edit indexer settings
   - Add tag: `flaresolverr`

### Jackett Integration
1. Go to Jackett admin panel
2. Add FlareSolverr URL: `http://localhost:8191/`
3. Configure specific indexers to use FlareSolverr

## Troubleshooting

### Service Not Responding
- Check if port 8191 is available: `netstat -tulpn | grep :8191`
- Verify container is running: `docker-compose ps`
- Check logs: `docker-compose logs flaresolverr`

### CloudFlare Challenges Still Failing
- Increase `maxTimeout` in requests (up to 60000ms)
- Try different user agents
- Consider using a CAPTCHA solver service
- Check if the target site has additional protections

### High Memory Usage
- FlareSolverr uses Chromium browser, which requires significant RAM
- Consider setting memory limits in compose file if needed
- Monitor with: `docker stats flaresolverr`

## Security Notes

- FlareSolverr runs a headless browser - keep it updated
- Limit network access to trusted applications only
- Monitor logs for suspicious activity
- Don't expose publicly without authentication

## Performance Tips

- Use FlareSolverr sparingly - it's resource intensive
- Configure appropriate timeouts
- Only tag indexers that actually need CloudFlare bypass
- Monitor resource usage regularly

## Health Check

Test if FlareSolverr is working:
```bash
curl -L -X POST 'http://localhost:8191/v1' \
-H 'Content-Type: application/json' \
--data-raw '{
    "cmd": "request.get",
    "url":"http://httpbin.org/ip"
}'
```

## Updates

```bash
docker-compose pull
docker-compose up -d
docker image prune
```