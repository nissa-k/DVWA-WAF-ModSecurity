# DVWA-WAF-ModSecurity
# Projets Réseau & Sécurité — Karadag Nissa

> Portfolio de travaux pratiques — Formation Bachelor Réseau & Sécurité  
> **Auteure :** Karadag Nissa

---

## 1 — DVWA & WAF ModSecurity

> Installation de DVWA & Mise en place d'un WAF — Décembre 2025

### Description

Mise en pratique du cycle complet de la sécurité web en deux phases : **exploitation** de vulnérabilités sur DVWA en environnement contrôlé, puis **protection** via un WAF ModSecurity avec le ruleset OWASP CRS.

### Environnement

```
Machine hôte (Windows)
        │
        │  SSH  → port 2222 → VM port 22
        │  HTTP → port 8080 → VM port 80
        ▼
VM Ubuntu Server 24.04 LTS — IP : 10.0.2.15 | RAM : 4 Go | CPU : 2 | Disque : 20 Go
  ├── Apache2 + PHP
  ├── MariaDB (base dvwa)
  ├── DVWA /var/www/html/
  └── ModSecurity 2 + OWASP CRS
```

### Phase offensive — Exploitation (mode Low)

| Vulnérabilité | Technique | Résultat |
|---|---|---|
| Command Injection | `10.0.2.15 ; whoami` | Exécution arbitraire → `www-data` |
| CSRF | Page HTML piégée, requête GET forcée | Mot de passe admin modifié |
| LFI | `?page=../../../../../etc/passwd` | Lecture complète de `/etc/passwd` |
| File Upload / RCE | Upload de `shell.php` | Exécution de commandes système |

### Phase défensive — Résultats WAF

| Attaque | Résultat |
|---|---|
| LFI | BLOQUÉ |
| XSS Reflected | BLOQUÉ |
| Command Injection | BLOQUÉ |
| File Upload PHP | BLOQUÉ |
| SQL Injection | DÉTECTÉ (log) |

---

## Fichiers

| Fichier | Projet |
|---|---|
| `compte_rendu_openbank.pdf` | OpenBank Infrastructure SI |
| `compte_rendu_DVWA_Nissa_Karadag.pdf` | DVWA & WAF ModSecurity |

---

*Formation Bachelor Réseau & Sécurité — Projets réalisés sous VirtualBox en environnement virtualisé et isolé.*
