# DVWA-WAF-ModSecurity
# Projets Réseau & Sécurité — Karadag Nissa

> Portfolio de travaux pratiques — Formation Bachelor Réseau & Sécurité  
> **Auteure :** Karadag Nissa

---

---

## 1 — OpenBank Infrastructure SI

> Raccordement SI & Sécurité bout en bout — `openbank.loc`

### Description

Mise en œuvre d'une infrastructure réseau multi-sites pour la société fictive **OpenBank**, simulant l'interconnexion sécurisée de deux sites distants (Paris et Nantes) avec Active Directory centralisé, tunnel VPN IPsec et firewalls Stormshield SNS.

### Architecture

```
Site Paris                               Site Nantes
──────────────────────                   ──────────────────────
SNS-PARIS (Stormshield EVA1)             SNS-NANTES (Stormshield EVA1)
  WAN : 192.36.253.10/24   ◄──VPN──►      WAN : 192.36.253.20/24
  LAN : 10.0.1.1/24                        LAN : 10.0.2.1/24

SRV-PARIS-DC (Windows Server 2022 GUI)  SRV-NANTES-RODC (Windows Server 2022 Core)
  IP : 10.0.1.10/24                        IP : 10.0.2.10/24
  Rôle : DC Principal                      Rôle : Contrôleur en lecture seule

Domaine Active Directory : openbank.loc
```

### Étapes réalisées

| Étape | Description |
|---|---|
| 1 | VirtualBox — réseau NAT `192.36.253.0/24`, import des 2 firewalls |
| 2 | Windows Server 2022 Paris — IP statique `10.0.1.10`, désactivation IPv6 |
| 3 | Active Directory — nouvelle forêt `openbank.loc`, OU, 4 groupes, 4 utilisateurs |
| 4 | RODC Nantes — installation Core, promotion PowerShell `-UseExistingAccount` |
| 5 | Stormshield — PKI inter-sites (RSA 4096), VPN IPsec IKEv2, filtrage, LDAP |

### Bilan

| Objectif | Statut |
|---|---|
| VirtualBox + firewalls SNS | OK |
| Windows Server 2022 Paris (GUI) | OK |
| Active Directory + domaine openbank.loc | OK |
| Structure OU + Groupes + Utilisateurs | OK |
| Windows Server 2022 Nantes (Core) | OK |
| RODC Nantes joint au domaine | OK |
| PKI — CA Paris & CA Nantes | OK |
| Certificats serveurs | OK |
| Règles de filtrage inter-sites | OK |
| Intégration LDAP / AD | OK |
| VPN IPsec site-à-site | Partiel — bug interne EVA |

### Problèmes rencontrés

- **Compatibilité navigateur** : Stormshield EVA 4.8.6 incompatible Chrome/Edge → résolu avec Firefox
- **RAM insuffisante** : augmentation à 4 Go / 4 vCPU pour les VMs firewall
- **Promotion RODC** : nécessite l'installation préalable du rôle AD DS + paramètre `-UseExistingAccount`
- **Tunnel VPN non établi** : incohérence interne EVA dans la gestion des certificats RSA

---

## 2 — DVWA & WAF ModSecurity

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
