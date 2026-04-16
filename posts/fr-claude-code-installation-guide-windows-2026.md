---
title: 'Guide d''installation de Claude Code sur Windows : Git, PATH, variables d''environnement, PowerShell, WSL et dépannage complet (2026)'
published: true
description: 'Un guide Windows très détaillé pour installer Claude Code pour les débutants. Couvre PowerShell, winget, Git for Windows, Node.js, PATH, variables d''environnement, invite de commande vs PowerShell vs WSL, et les problèmes courants après l''installation.'
tags: 'ai, cli, tutorial, windows'
canonical_url: null
cover_image: 'https://lsky.zhongzhuan.chat/i/2025/12/29/69523b0cd9f0d.png'
---

Les utilisateurs Windows rencontrent généralement une série de problèmes différents de ceux des utilisateurs macOS avec Claude Code.

Non pas parce que Claude Code est impossible sur Windows, mais parce que Windows propose plus de combinaisons d'environnements :

- Invite de commande
- PowerShell
- Windows Terminal
- Git Bash
- WSL
- Node installé à partir d'un MSI
- Node installé à partir de winget
- Git installé mais non ajouté au PATH
- Variables d'environnement définies pour un shell mais pas pour un autre

C'est pourquoi ce guide procède plus lentement et explique la chaîne de configuration à partir de zéro.

## Avant d'installer quoi que ce soit : choisissez votre approche Windows

Il y a deux chemins réalistes.

### Option A : Configuration Windows native

Utilisez :
- Windows Terminal
- PowerShell
- Git for Windows
- Node.js for Windows
- npm install global

C'est plus facile pour la plupart des débutants.

### Option B : Configuration WSL

Utilisez :
- WSL2
- Ubuntu ou Debian à l'intérieur de WSL
- Étapes d'installation Linux à l'intérieur du shell WSL

C'est souvent plus propre pour les développeurs, mais cela ajoute une couche supplémentaire. Si vous êtes complètement nouveau, commencez par Windows natif en premier, sauf si vous utilisez déjà WSL.

## Configuration recommandée pour les débutants

Pour la plupart des nouveaux utilisateurs, je recommande :

- Windows 11 ou Windows 10 à jour
- Windows Terminal
- PowerShell
- winget pour les installations de packages
- Git for Windows
- Node.js LTS
- Claude Code via npm

## Étape 1 : Ouvrez le bon terminal

Installez ou lancez **Windows Terminal** si possible.

Ensuite, ouvrez **PowerShell**.

Vérifiez dans quel shell vous vous trouvez :

```powershell
$PSVersionTable.PSVersion
```

Si PowerShell s'ouvre et fonctionne, restez-y pour toute la configuration. Ne mélangez pas PowerShell, cmd, Git Bash et WSL lors de l'installation initiale, sauf si vous savez pourquoi.

## Étape 2 : Vérifiez si `winget` est disponible

`winget` est le moyen le plus facile d'installer Git et Node sur Windows moderne.

```powershell
winget --version
```

Si cela fonctionne, c'est super.

Si ce n'est pas le cas :
- mettez à jour App Installer depuis Microsoft Store
- ou installez les packages manuellement depuis les sites officiels

## Étape 3 : Installez Git

Vérifiez d'abord :

```powershell
git --version
```

Si Git manque, installez-le avec winget :

```powershell
winget install --id Git.Git -e --source winget
```

Ensuite, **fermez et rouvrez PowerShell**.

Vérifiez :

```powershell
git --version
where.exe git
```

### Pourquoi Git est important sous Windows aussi

Pour la même raison que sur macOS :
- workflow conscient du repo
- diffs
- éditions plus sûres
- rollback de version
- de nombreux outils de développement supposent que Git est installé

Si vous n'avez pas encore de repo :

```powershell
mkdir $HOME\Projects\claude-code-test -Force
cd $HOME\Projects\claude-code-test
git init
```

Identité globale optionnelle :

```powershell
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

## Étape 4 : Installez Node.js et npm

Vérifiez les versions existantes :

```powershell
node --version
npm --version
```

Si manquant, installez Node.js :

```powershell
winget install --id OpenJS.NodeJS.LTS -e --source winget
```

Fermez et rouvrez PowerShell à nouveau.

Vérifiez :

```powershell
node --version
npm --version
where.exe node
where.exe npm
```

## Étape 5 : Installez Claude Code

Vérifiez s'il existe déjà :

```powershell
where.exe claude
claude --version
```

Si non, installez-le globalement :

```powershell
npm install -g @anthropic-ai/claude-code
```

Ensuite, vérifiez :

```powershell
where.exe claude
claude --version
```

## Étape 6 : Corrigez les problèmes de PATH sous Windows

La plainte la plus courante sous Windows est :

- npm install dit succès
- mais `claude` n'est toujours pas reconnu

Erreur typique :

```powershell
claude : The term 'claude' is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### D'abord, trouvez le préfixe global de npm

```powershell
npm config get prefix
```

Cela pointe généralement vers un répertoire dont l'emplacement `bin` ou exécutable devrait être découvrable via PATH.

Vérifiez où npm a installé `claude` :

```powershell
npm list -g --depth=0
```

Essayez également :

```powershell
Get-Command claude -ErrorAction SilentlyContinue
```

### Correctif courant : rouvrez le shell

Beaucoup de problèmes de PATH sont simplement des sessions obsolètes. Fermez complètement PowerShell, ouvrez-en un nouveau, et réessayez.

### Si PATH est toujours incorrect

Inspectez le PATH utilisateur :

```powershell
[Environment]::GetEnvironmentVariable("Path", "User")
```

Et le PATH machine :

```powershell
[Environment]::GetEnvironmentVariable("Path", "Machine")
```

Si l'emplacement exécutable global de npm manque, vous pouvez l'ajouter via l'interface Variables d'environnement Windows ou avec PowerShell.

Soyez prudent lors de l'édition programmatique de PATH. Faites d'abord une sauvegarde.

## Étape 7 : Définissez correctement les variables d'environnement

Les utilisateurs Windows définissent souvent les variables à un endroit et supposent que chaque shell les verra. Ce n'est pas toujours vrai.

### Variable de session uniquement dans PowerShell

```powershell
$env:ANTHROPIC_API_KEY = "your_key_here"
$env:OPENAI_API_KEY = "your_crazyrouter_key"
$env:OPENAI_BASE_URL = "https://crazyrouter.com/v1"
```

Celles-ci ne durent que pour la session actuelle.

### Variables d'environnement persistantes au niveau utilisateur

```powershell
[Environment]::SetEnvironmentVariable("ANTHROPIC_API_KEY", "your_key_here", "User")
[Environment]::SetEnvironmentVariable("OPENAI_API_KEY", "your_crazyrouter_key", "User")
[Environment]::SetEnvironmentVariable("OPENAI_BASE_URL", "https://crazyrouter.com/v1", "User")
```

Ensuite, fermez et rouvrez PowerShell.

Vérifiez :

```powershell
echo $env:ANTHROPIC_API_KEY
echo $env:OPENAI_API_KEY
echo $env:OPENAI_BASE_URL
```

## Étape 8 : Comprenez la différence entre PowerShell, cmd, Git Bash et WSL

Cela importe car les variables et le PATH peuvent se comporter différemment.

| Environnement | Bon pour les débutants ? | Notes |
|------------|---------------------|-------|
| PowerShell | Oui | Meilleur choix Windows natif |
| Invite de commande | Acceptable | Moins pratique que PowerShell |
| Git Bash | Mitigé | Fonctionne, mais ajoute une autre couche de shell |
| WSL | Bon pour les développeurs | Meilleur si vous voulez un comportement de type Linux |

Si vous avez installé Claude Code dans PowerShell Windows natif, ne le testez pas d'abord dans WSL et supposez que le même environnement s'applique.

WSL a son propre système de packages, ses propres chemins, fichiers shell et variables.

## Étape 9 : Chemin WSL optionnel

Si vous voulez l'environnement de développeur le plus propre à long terme sur Windows, installez WSL.

Vérifiez WSL :

```powershell
wsl --status
```

Installez si nécessaire :

```powershell
wsl --install
```

Ensuite, redémarrez si Windows le demande.

Après cela, ouvrez Ubuntu et traitez-le comme une machine Linux :

- installez Git à l'intérieur de WSL
- installez Node à l'intérieur de WSL
- installez Claude Code à l'intérieur de WSL
- définissez les variables d'environnement à l'intérieur des fichiers shell WSL

Ne supposez pas que votre installation Node côté Windows couvre automatiquement WSL.

## Étape 10 : Vérifiez tout de bout en bout

Exécutez ces vérifications :

```powershell
git --version
node --version
npm --version
claude --version
where.exe git
where.exe node
where.exe npm
where.exe claude
```

Ensuite, créez un dossier de test sûr :

```powershell
mkdir $HOME\Projects\claude-code-test -Force
cd $HOME\Projects\claude-code-test
if (-not (Test-Path .git)) { git init }
"# test" | Out-File README.md -Encoding utf8
```

Et testez d'abord le CLI avec des commandes non destructives.

## Problèmes courants sous Windows et solutions

## 1. `claude` n'est pas reconnu

Cause :
- répertoire exécutable global de npm pas dans PATH
- shell non redémarré
- l'installation a échoué

Solution :
- rouvrez PowerShell
- vérifiez `npm config get prefix`
- confirmez que le package existe dans la liste npm globale
- inspectez PATH

## 2. Git s'installe mais PowerShell ne le trouve toujours pas

Cause :
- session de terminal ouverte avant l'installation
- PATH non rafraîchi

Solution :
- fermez complètement et rouvrez le terminal
- vérifiez avec `where.exe git`

## 3. Node s'installe, mais npm manque ou est cassé

Cause :
- installation incomplète
- version Node ancienne conflictuelle

Solution :
- désinstallez les versions Node conflictuelles si nécessaire
- réinstallez LTS correctement
- vérifiez à la fois `node --version` et `npm --version`

## 4. La variable d'environnement est définie dans PowerShell mais pas dans un autre terminal

Cause :
- la variable était de session uniquement

Solution :
- utilisez des variables d'environnement persistantes au niveau utilisateur
- rouvrez le terminal après les avoir définies

## 5. WSL fonctionne mais PowerShell ne fonctionne pas, ou vice versa

Cause :
- vous avez configuré deux environnements différents

Solution :
- décidez si vous voulez Windows natif ou WSL comme environnement principal de Claude Code
- complétez complètement la configuration à l'intérieur de cet environnement

## 6. Le proxy d'entreprise bloque l'installation npm

Vous pourriez avoir besoin de :

```powershell
npm config set proxy http://proxy.example.com:8080
npm config set https-proxy http://proxy.example.com:8080
```

Et possiblement aussi des variables de session.

## 7. L'antivirus ou le logiciel de sécurité interfère

Parfois, les outils de sécurité interfèrent avec les outils CLI fraîchement installés ou les scripts.

Si les journaux d'installation semblent normaux mais que les exécutables ne se comportent pas normalement, testez dans un terminal propre, confirmez que le fichier existe, et vérifiez Windows Defender ou l'historique de la protection des points de terminaison.

## Une configuration Windows sûre par défaut

Si vous voulez le chemin le plus simple qui est le plus facile à supporter, utilisez exactement cette pile :

- Windows Terminal
- PowerShell
- winget
- Git for Windows
- Node.js LTS
- Claude Code via npm install global
- variables d'environnement persistantes au niveau utilisateur

Cette configuration est ennuyeuse, et c'est exactement pour cela qu'elle est bonne.

## FAQ

### Les débutants devraient-ils utiliser PowerShell ou WSL pour Claude Code ?

Si vous êtes nouveau, commencez avec PowerShell. Si vous préférez déjà les outils Linux ou utilisez déjà WSL quotidiennement, WSL peut être plus propre à long terme.

### Pourquoi Claude Code s'est-il installé avec succès mais ne s'exécute toujours pas ?

Le plus souvent : PATH obsolète, mauvais shell, ou npm a installé le package dans un emplacement que votre terminal actuel ne lit pas.

### Ai-je besoin de Git avant d'utiliser Claude Code sous Windows ?

Pour une utilisation sérieuse, oui. Même si le CLI démarre sans Git, les workflows de codage normaux sont beaucoup plus fluides avec Git installé et configuré.

### Où dois-je stocker les variables d'environnement de Claude Code sous Windows ?

Pour la persistance, définissez-les au niveau de l'environnement utilisateur, pas seulement la session shell actuelle.

### Git Bash est-il un bon endroit pour exécuter Claude Code ?

Cela peut fonctionner, mais pour les débutants, cela ajoute plus de variables. PowerShell est plus simple à documenter et à supporter.

## Conclusion

L'histoire d'installation Windows n'est pas difficile parce que Claude Code lui-même est difficile. C'est difficile parce que Windows vous donne de nombreux environnements qui se chevauchent.

Si vous maintenez la configuration cohérente — PowerShell, winget, Git, Node, npm, Claude Code, puis variables d'environnement — l'installation devient beaucoup plus facile à déboguer et à enseigner.
