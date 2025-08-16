# DNS Configuration - Five Rings Photography

**Domain Registrar**: [Your registrar - update this]  
**DNS Provider**: [Your DNS provider - update this]  
**Last Verified**: 2025-08-16  

## 📍 Current Production DNS Records

### Root Domain: fiverings.photo

#### A Records (Required for GitHub Pages)
| Type | Host | Value | TTL | Purpose | Status |
|------|------|-------|-----|---------|--------|
| A | @ | 185.199.108.153 | 3600 | GitHub Pages IP | ✅ Active |
| A | @ | 185.199.109.153 | 3600 | GitHub Pages IP | ✅ Active |
| A | @ | 185.199.110.153 | 3600 | GitHub Pages IP | ✅ Active |
| A | @ | 185.199.111.153 | 3600 | GitHub Pages IP | ✅ Active |

#### CNAME Records
| Type | Host | Value | TTL | Purpose | Status |
|------|------|-------|-----|---------|--------|
| CNAME | www | jur1st.github.io | 3600 | www redirect | ⚠️ Optional |
| CNAME | antisocial | jur1st.github.io | 3600 | Gallery subdomain | ✅ Active |

### Important Notes
- All four A records are required for redundancy
- TTL of 3600 (1 hour) allows for reasonable propagation
- Do NOT use A records for subdomains - use CNAME instead

## 🔮 Future Subdomain Planning

### Reserved for Future Services
| Subdomain | Purpose | Status |
|-----------|---------|--------|
| clients.fiverings.photo | Client gallery portal | 📅 Planned |
| book.fiverings.photo | Booking system | 📅 Planned |
| shop.fiverings.photo | Print sales | 📅 Planned |
| api.fiverings.photo | API endpoint | 💭 Considering |

### To Add a New Subdomain
1. Add CNAME record: `subdomain` → `jur1st.github.io`
2. Create new GitHub repo for subdomain
3. Configure GitHub Pages with custom domain
4. Deploy content

## ✅ DNS Verification Commands

### Quick Verification
```bash
# Check root domain A records
dig fiverings.photo +short
# Should return:
# 185.199.108.153
# 185.199.109.153
# 185.199.110.153
# 185.199.111.153

# Check subdomain CNAME
dig antisocial.fiverings.photo +short
# Should return:
# jur1st.github.io.
# [GitHub Pages IPs]
```

### Detailed DNS Lookup
```bash
# Full DNS record details
dig fiverings.photo ANY

# Trace DNS resolution path
dig +trace fiverings.photo

# Check specific nameserver
dig @8.8.8.8 fiverings.photo
```

### Test DNS Propagation
```bash
# Check from multiple locations
curl -s "https://dns.google/resolve?name=fiverings.photo&type=A" | jq

# Alternative DNS checkers
nslookup fiverings.photo 8.8.8.8  # Google DNS
nslookup fiverings.photo 1.1.1.1  # Cloudflare DNS
```

## 🚨 Troubleshooting DNS Issues

### DNS Not Resolving
1. **Verify records in DNS provider**
   - Login to DNS provider dashboard
   - Check all A records are present
   - Ensure no conflicting records

2. **Check propagation time**
   - DNS changes can take 1-48 hours
   - Use online propagation checker
   - Clear local DNS cache

3. **Clear DNS cache locally**
   ```bash
   # macOS
   sudo dscacheutil -flushcache
   
   # Alternative
   sudo killall -HUP mDNSResponder
   ```

### Wrong IP Addresses Returned
- Ensure no proxy/CDN service is enabled (like Cloudflare proxy)
- Check for duplicate A records
- Verify TTL hasn't cached old values

### Subdomain Not Working
- CNAME must point to `jur1st.github.io` (not the parent domain)
- Cannot have both A and CNAME records for same subdomain
- Subdomain repo must have Pages enabled

## 📊 DNS Architecture Diagram

```
┌─────────────────┐
│  DNS Provider   │
└────────┬────────┘
         │
    ┌────▼────┐
    │ Root DNS│
    └────┬────┘
         │
┌────────▼────────────────────┐
│    fiverings.photo          │
│  A: 185.199.108-111.153     │
└────────┬────────────────────┘
         │
    ┌────┴──────────────┬──────────────┐
    │                   │              │
┌───▼────┐      ┌───────▼──────┐  ┌───▼───┐
│   www  │      │  antisocial   │  │ future│
│ CNAME  │      │    CNAME      │  │ CNAME │
└────────┘      └───────────────┘  └───────┘
     │                  │               │
     └──────────────────┼───────────────┘
                        │
                ┌───────▼────────┐
                │jur1st.github.io│
                │ GitHub Pages   │
                └────────────────┘
```

## 🔐 Security Considerations

### DNSSEC
- Consider enabling DNSSEC if provider supports it
- Adds cryptographic signatures to DNS records
- Prevents DNS hijacking attacks

### CAA Records (Optional)
```
CAA @ 0 issue "letsencrypt.org"
```
- Restricts which CAs can issue certificates
- GitHub Pages uses Let's Encrypt

## 📝 DNS Change Log

| Date | Change | Reason | By |
|------|--------|--------|-----|
| 2025-08-16 | Initial A records configured | Setup GitHub Pages | John |
| 2025-08-16 | Added antisocial CNAME | Gallery subdomain | John |
| [Date] | [Future changes] | [Reason] | [Who] |

---

**Configuration Status**: ✅ Verified Working  
**SSL Status**: ✅ Certificates Active  
**Last Check**: 2025-08-16  

For GitHub Pages configuration, see [GITHUB-PAGES.md](GITHUB-PAGES.md)