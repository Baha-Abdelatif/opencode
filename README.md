# OpenCode : fork qui suit l'officiel

Fork de [anomalyco/opencode](https://github.com/anomalyco/opencode) qui republie chaque version officielle 2.x
avec un petit correctif MCP OAuth, pour que les serveurs MCP Google (Gmail, Drive, Agenda), Abby et Airtable
restent connectés.

## Ce que contient le correctif (branche `mcp-oauth-fixes`)

- **Accès hors ligne Google** : l'adresse de connexion Google demande `access_type=offline` et `prompt=consent`.
  Sans cela, Google ne fournit pas de jeton de renouvellement et la connexion expire au bout d'une heure
  (officiel : issue #59820).
- **Connexion proposée quand un outil est refusé** : reprise de la PR officielle #55013. Un serveur qui accepte la
  connexion anonyme mais refuse les appels d'outils (cas Google) passe en « authentification requise », et
  l'interface propose alors la connexion (officiel : issue #53942).

## Comment ça tourne

- Cette branche (`fork-ci`, branche par défaut) ne contient que l'automatisation.
- `.github/workflows/suivre-officiel.yml` vérifie toutes les 3 heures la dernière version 2.x publiée. Pour une
  version nouvelle : rejoue le correctif dessus, lance les tests MCP, construit le binaire Linux x64 et publie
  la release `vX.Y.Z-fork` (archive, empreintes SHA-256, correctif appliqué).
- Conflit ou échec : une issue « Fork : échec du suivi de l'officiel » est ouverte, rien n'est publié.
- `etat/derniere-version.txt` : dernière release publiée par le workflow.

## Régler un conflit

```bash
cd ~/src/opencode
git fetch upstream 'refs/tags/v2.*:refs/tags/v2.*'
git rebase --onto vX.Y.Z <ancien-tag> mcp-oauth-fixes   # résoudre, puis git rebase --continue
git push --no-verify --force-with-lease origin mcp-oauth-fixes   # le contrôle local du monorepo plante sur turbo
gh workflow run suivre-officiel.yml -R Baha-Abdelatif/opencode
```

Quand l'officiel intègre un des correctifs, le commit correspondant disparaît seul au rejeu (devenu vide).

## Installer sur un poste

Commande locale : `installer-opencode-fork` (script `~/.claude/scripts/installer-opencode-fork.sh`).
