# Git Hooks

## Pre-Commit Hook: CodeScene Check

Il repository ha un pre-commit hook che verifica la qualità del codice prima di ogni commit.

### Cosa Fa

1. **Analizza file staged** — controlla solo TypeScript/Rust modificati
2. **Confronta con base branch** — `origin/main` per branch, `HEAD~1` per main
3. **Avvisa per file grandi** — >500 linee modificate
4. **Suggerisce review** — con Claude Code + CodeScene MCP per analisi dettagliata

### Bypass Hook

Se sai cosa stai facendo:

```bash
# Skip hook per questo commit
git commit --no-verify -m "your message"

# O includi nel commit message
git commit -m "your message [skip codescene]"
```

### Installazione (già fatto per questa repo)

L'hook è già installato in `.git/hooks/pre-commit`.

Se cloni la repo altrove, copia l'hook:
```bash
cp .github/hooks/pre-commit .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit
```

### Esempio Output

#### ✅ Commit Normale
```
🔍 Running CodeScene Code Health check...
   Comparing against: origin/main
   Analyzing code changes...
✅ CodeScene check passed
   +42 -18 lines

   💡 For detailed code health analysis, run:
      claude 'Check code health of this commit with CodeScene MCP'
```

#### ⚠️ File Grandi
```
🔍 Running CodeScene Code Health check...
   Comparing against: origin/main
   Analyzing code changes...
⚠️  Large file changes detected (>500 lines):
   - src/components/Editor.tsx
   - src-tauri/src/vault.rs

   Consider:
   - Breaking into smaller commits
   - Reviewing with Claude Code + CodeScene MCP
   - Running: claude 'Review code health of staged changes'

   Continue anyway? (y/N) 
```

### CodeScene MCP Integration

Per analisi dettagliata del code health, usa Claude Code:

```bash
# Analizza staged changes
claude 'Check code health of staged changes with CodeScene MCP'

# Analizza file specifico
claude 'What is the code health score of src/components/Editor.tsx?'

# Pre-commit safeguard
claude 'Run pre_commit_code_health_safeguard on staged changes'
```

### Troubleshooting

**Hook non si attiva:**
- Verifica che `.git/hooks/pre-commit` esista ed sia eseguibile
- `ls -la .git/hooks/pre-commit` — dovrebbe mostrare `-rwxr-xr-x`

**Vuoi disabilitare temporaneamente:**
```bash
mv .git/hooks/pre-commit .git/hooks/pre-commit.disabled
```

**Vuoi riabilitare:**
```bash
mv .git/hooks/pre-commit.disabled .git/hooks/pre-commit
```

### Future Improvements

Possibili miglioramenti:
- [ ] Integrazione diretta API CodeScene per score numerico
- [ ] Fail automatico se code health < soglia
- [ ] Cache dei risultati per evitare re-analisi
- [ ] Hook pre-push più pesante per analisi completa
