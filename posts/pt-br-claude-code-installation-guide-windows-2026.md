---
title: 'Guia de instalação do Claude Code no Windows: Git, PATH, variáveis de ambiente, PowerShell, WSL e solução completa de problemas (2026)'
published: true
description: 'Um guia muito detalhado para Windows sobre como instalar Claude Code para iniciantes. Cobre PowerShell, winget, Git for Windows, Node.js, PATH, variáveis de ambiente, prompt de comando vs PowerShell vs WSL e problemas comuns após a instalação.'
tags: 'ai, cli, tutorial, windows'
canonical_url: null
cover_image: 'https://lsky.zhongzhuan.chat/i/2025/12/29/69523b0cd9f0d.png'
id: 3511065
date: '2026-04-16T14:53:50Z'
---

Usuários do Windows geralmente enfrentam um conjunto diferente de problemas com Claude Code em comparação com usuários de macOS.

Não porque Claude Code seja impossível no Windows, mas porque o Windows tem mais combinações de ambientes:

- Command Prompt
- PowerShell
- Windows Terminal
- Git Bash
- WSL
- Node instalado via MSI
- Node instalado via winget
- Git instalado mas não adicionado ao PATH
- variáveis de ambiente configuradas para um shell mas não para outro

É por isso que este guia segue mais lentamente e explica toda a cadeia de configuração do zero.

## Antes de Instalar Qualquer Coisa: Escolha Seu Caminho no Windows

Existem dois caminhos realistas.

### Opção A: Configuração nativa do Windows

Use:
- Windows Terminal
- PowerShell
- Git for Windows
- Node.js for Windows
- instalação global npm

Isso é mais fácil para a maioria dos iniciantes.

### Opção B: Configuração WSL

Use:
- WSL2
- Ubuntu ou Debian dentro do WSL
- passos de instalação Linux dentro do WSL

Isso é frequentemente mais limpo para desenvolvedores, mas adiciona uma camada extra. Se você é totalmente novo, comece com o Windows nativo primeiro, a menos que já use WSL.

## Configuração Recomendada para Iniciantes

Para a maioria dos novos usuários, recomendo:

- Windows 11 ou Windows 10 atualizado
- Windows Terminal
- PowerShell
- winget para instalar pacotes
- Git for Windows
- Node.js LTS
- Claude Code via npm

## Passo 1: Abra o Terminal Correto

Instale ou inicie o **Windows Terminal** se possível.

Depois abra o **PowerShell**.

Verifique em qual shell você está:

```powershell
$PSVersionTable.PSVersion
```

Se PowerShell abrir e funcionar, fique nele durante toda a configuração. Não misture PowerShell, cmd, Git Bash e WSL durante a instalação inicial, a menos que você saiba o motivo.

## Passo 2: Verifique Se `winget` Está Disponível

`winget` é a forma mais fácil de instalar Git e Node no Windows moderno.

```powershell
winget --version
```

Se funcionar, ótimo.

Se não funcionar:
- atualize App Installer da Microsoft Store
- ou instale pacotes manualmente dos sites oficiais

## Passo 3: Instale Git

Verifique primeiro:

```powershell
git --version
```

Se Git estiver faltando, instale com winget:

```powershell
winget install --id Git.Git -e --source winget
```

Depois **feche e reabra PowerShell**.

Verifique:

```powershell
git --version
where.exe git
```

### Por que Git importa também no Windows

Pela mesma razão que no macOS:
- fluxo de trabalho ciente de repositório
- diffs
- edições mais seguras
- rollback de versão
- muitas ferramentas de desenvolvedor assumem Git

Se você ainda não tem um repositório:

```powershell
mkdir $HOME\Projects\claude-code-test -Force
cd $HOME\Projects\claude-code-test
git init
```

Identidade global opcional:

```powershell
git config --global user.name "Seu Nome"
git config --global user.email "voce@exemplo.com"
```

## Passo 4: Instale Node.js e npm

Verifique as versões existentes:

```powershell
node --version
npm --version
```

Se estiver faltando, instale Node.js:

```powershell
winget install --id OpenJS.NodeJS.LTS -e --source winget
```

Feche e reabra PowerShell novamente.

Verifique:

```powershell
node --version
npm --version
where.exe node
where.exe npm
```

## Passo 5: Instale Claude Code

Verifique se já existe:

```powershell
where.exe claude
claude --version
```

Se não existir, instale globalmente:

```powershell
npm install -g @anthropic-ai/claude-code
```

Depois verifique:

```powershell
where.exe claude
claude --version
```

## Passo 6: Corrija Problemas de PATH no Windows

A reclamação mais comum no Windows é:

- npm install diz sucesso
- mas `claude` ainda não é reconhecido

Erro típico:

```powershell
claude : The term 'claude' is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### Primeiro, encontre o prefixo global npm

```powershell
npm config get prefix
```

Normalmente isso aponta para um diretório cujo local `bin` ou executável deve ser descoberto via PATH.

Verifique onde npm instalou `claude`:

```powershell
npm list -g --depth=0
```

Tente também:

```powershell
Get-Command claude -ErrorAction SilentlyContinue
```

### Correção comum: reabra o shell

Muitos problemas de PATH são apenas sessões antigas. Feche PowerShell completamente, abra uma nova e tente novamente.

### Se PATH ainda estiver errado

Inspecione o PATH do usuário:

```powershell
[Environment]::GetEnvironmentVariable("Path", "User")
```

E o PATH da máquina:

```powershell
[Environment]::GetEnvironmentVariable("Path", "Machine")
```

Se a localização do executável global npm estiver ausente, você pode adicioná-la pela interface de Variáveis de Ambiente do Windows ou com PowerShell.

Tenha cuidado ao editar PATH programaticamente. Faça backup primeiro.

## Passo 7: Configure Variáveis de Ambiente Corretamente

Usuários do Windows frequentemente definem variáveis em um local e assumem que cada shell as verá. Nem sempre é assim.

### Variável somente na sessão em PowerShell

```powershell
$env:ANTHROPIC_API_KEY = "sua_chave_aqui"
$env:OPENAI_API_KEY = "sua_chave_crazyrouter"
$env:OPENAI_BASE_URL = "https://crazyrouter.com/v1"
```

Essas durão apenas para a sessão atual.

### Variáveis de ambiente persistentes no nível do usuário

```powershell
[Environment]::SetEnvironmentVariable("ANTHROPIC_API_KEY", "sua_chave_aqui", "User")
[Environment]::SetEnvironmentVariable("OPENAI_API_KEY", "sua_chave_crazyrouter", "User")
[Environment]::SetEnvironmentVariable("OPENAI_BASE_URL", "https://crazyrouter.com/v1", "User")
```

Depois feche e reabra PowerShell.

Verifique:

```powershell
echo $env:ANTHROPIC_API_KEY
echo $env:OPENAI_API_KEY
echo $env:OPENAI_BASE_URL
```

## Passo 8: Entenda a Diferença Entre PowerShell, cmd, Git Bash e WSL

Isso importa porque variáveis e PATH podem se comportar de forma diferente.

| Ambiente | Bom para Iniciantes? | Notas |
|------------|---------------------|-------|
| PowerShell | Sim | Melhor escolha nativa do Windows |
| Command Prompt | Okay | Menos conveniente que PowerShell |
| Git Bash | Misto | Funciona, mas adiciona outra camada de shell |
| WSL | Bom para desenvolvedores | Melhor se você quer comportamento tipo Linux |

Se você instalou Claude Code no PowerShell nativo do Windows, não o teste primeiro no WSL e assuma que o mesmo ambiente se aplica.

WSL tem seu próprio sistema de pacotes, caminhos, arquivos de shell e variáveis.

## Passo 9: Caminho Opcional WSL

Se você quer o ambiente de desenvolvedor mais limpo em longo prazo no Windows, instale WSL.

Verifique WSL:

```powershell
wsl --status
```

Instale se necessário:

```powershell
wsl --install
```

Depois reinicie se Windows pedir.

Depois disso, abra Ubuntu e trate como uma máquina Linux:

- instale Git dentro do WSL
- instale Node dentro do WSL
- instale Claude Code dentro do WSL
- configure variáveis de ambiente dentro dos arquivos de shell do WSL

Não assuma que sua instalação do Node no lado do Windows cobre automaticamente o WSL.

## Passo 10: Verifique Tudo de Ponta a Ponta

Execute estas verificações:

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

Depois crie uma pasta de teste segura:

```powershell
mkdir $HOME\Projects\claude-code-test -Force
cd $HOME\Projects\claude-code-test
if (-not (Test-Path .git)) { git init }
"# test" | Out-File README.md -Encoding utf8
```

E teste a CLI com comandos não destrutivos primeiro.

## Problemas Comuns do Windows e Correções

## 1. `claude` não é reconhecido

Causa:
- diretório executável global npm não está em PATH
- shell não foi reiniciado
- instalação falhou

Correção:
- reabra PowerShell
- verifique `npm config get prefix`
- confirme que o pacote existe na lista npm global
- inspecione PATH

## 2. Git instala mas PowerShell ainda não consegue encontrá-lo

Causa:
- sessão de terminal aberta antes da instalação
- PATH não foi atualizado

Correção:
- feche e reabra completamente o terminal
- verifique com `where.exe git`

## 3. Node instala, mas npm está faltando ou quebrado

Causa:
- instalação incompleta
- versão antiga conflitante do Node

Correção:
- desinstale versões conflitantes do Node se necessário
- reinstale LTS limpo
- verifique ambos `node --version` e `npm --version`

## 4. Variável de ambiente é definida em PowerShell mas não em outro terminal

Causa:
- variável era somente na sessão

Correção:
- use variáveis de ambiente persistentes no nível do usuário
- reabra o terminal após configurá-las

## 5. WSL funciona mas PowerShell não, ou vice-versa

Causa:
- você configurou dois ambientes diferentes

Correção:
- decida se você quer Windows nativo ou WSL como seu ambiente Claude Code principal
- complete a configuração completamente dentro desse ambiente

## 6. Proxy corporativo bloqueia instalação npm

Você pode precisar:

```powershell
npm config set proxy http://proxy.exemplo.com:8080
npm config set https-proxy http://proxy.exemplo.com:8080
```

E possivelmente também variáveis de sessão.

## 7. Antivírus ou software de segurança interfere

Às vezes, ferramentas de segurança interferem com ferramentas CLI ou scripts recém-instalados.

Se os logs de instalação parecerem normais mas os executáveis não se comportarem normalmente, teste em um terminal limpo, confirme que o arquivo existe e verifique o Histórico de Segurança do Windows ou proteção de endpoint.

## Uma Configuração Segura Padrão do Windows

Se você quer o caminho mais simples que é mais fácil de suportar, use exatamente esta pilha:

- Windows Terminal
- PowerShell
- winget
- Git for Windows
- Node.js LTS
- Claude Code via instalação global npm
- variáveis de ambiente persistentes do usuário

Essa configuração é chata, e é exatamente por isso que é boa.

## FAQ

### Iniciantes devem usar PowerShell ou WSL para Claude Code?

Se você é novo, comece com PowerShell. Se você já prefere ferramentas Linux ou já usa WSL diariamente, WSL pode ser mais limpo em longo prazo.

### Por que Claude Code instalou com sucesso mas ainda não funciona?

Na maioria das vezes: PATH obsoleto, shell errado ou npm instalou o pacote em um local que seu terminal atual não está lendo.

### Preciso de Git antes de usar Claude Code no Windows?

Para uso sério, sim. Mesmo que a CLI inicie sem Git, os fluxos de trabalho de codificação normal são muito mais suaves com Git instalado e configurado.

### Onde devo armazenar variáveis de ambiente Claude Code no Windows?

Para persistência, defina-as no nível de ambiente do usuário, não apenas na sessão do shell atual.

### Git Bash é um bom lugar para executar Claude Code?

Pode funcionar, mas para iniciantes adiciona mais variáveis. PowerShell é mais simples de documentar e suportar.

## Conclusão Final

A história de instalação do Windows não é difícil porque Claude Code em si é difícil. É difícil porque o Windows oferece muitos ambientes sobrepostos.

Se você manter a configuração consistente — PowerShell, winget, Git, Node, npm, Claude Code, então variáveis de ambiente — a instalação fica muito mais fácil de debugar e ensinar.
