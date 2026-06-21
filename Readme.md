<div align="center">

```
╔═══════════════════════════════════════════════╗
║              J W T F O R G E   B R              ║
║        Exploração de JSON Web Tokens · v1.0     ║
║     CyberGuard Academy · github.com/Kalyel473   ║
╚═══════════════════════════════════════════════╝
```

![versão](https://img.shields.io/badge/vers%C3%A3o-1.0-00FF6A)
![python](https://img.shields.io/badge/python-3.8%2B-blue)
![licença](https://img.shields.io/badge/licen%C3%A7a-MIT-green)
![plataforma](https://img.shields.io/badge/SO-Windows%20%7C%20Linux%20%7C%20Termux-lightgrey)

**Ferramenta CLI educacional para entender e explorar as principais falhas de JSON Web Tokens.**

</div>

---

## ⚠️ Aviso Legal

Esta ferramenta foi criada **exclusivamente** para fins **educacionais** e para testes de segurança em sistemas **próprios** ou nos quais você possui **autorização formal e por escrito**.

O uso não autorizado contra sistemas de terceiros é **crime** no Brasil, previsto na **Lei nº 12.737/2012 (Lei Carolina Dieckmann)** e na **Lei nº 13.709/2018 (LGPD)**. O autor e o projeto CyberGuard Academy **não se responsabilizam** pelo uso indevido desta ferramenta.

---

## 🎯 O que é

O **JWTForge BR** é uma ferramenta de linha de comando que demonstra, de forma **didática**, como funcionam (e como são exploradas) as falhas mais comuns em JWT. Cada módulo explica o ataque antes de executá-lo (**📖 COMO FUNCIONA**) e fecha com o lado defensivo (**🛡️ MITIGAÇÃO**) — você aprende a atacar **e** a se defender.

**Para quem é:** estudantes de pentest, bug bounty e segurança ofensiva que querem entender JWT na prática, não só decorar comandos.

Toda a criptografia é feita com a **biblioteca padrão do Python** (`hmac`/`hashlib`/`base64`). A ferramenta **não usa PyJWT de propósito** — bibliotecas de produção bloqueiam ataques como `alg:none`, o que impediria o aprendizado.

---

## 🔓 Vulnerabilidades cobertas

| # | Módulo | O que faz |
|---|--------|-----------|
| 1 | **Decodificar e inspecionar** | Lê header/payload sem a chave, analisa expiração e aponta fraquezas. |
| 2 | **Ataque `alg:none`** | Remove a assinatura e forja o payload; gera variações de caixa (`none`/`None`/`NONE`/`nOnE`). |
| 3 | **Bruteforce de secret (HS256)** | Ataque de dicionário multithread contra secrets fracas. |
| 4 | **Confusão de algoritmo (RS256 → HS256)** | Assina um token HS256 usando a chave pública RSA como secret. |
| 5 | **Injeção no header `kid`** | Gera tokens com `kid` malicioso (path traversal, SQLi, command injection). |
| 6 | **Forjar token** | Com a secret em mãos, cria qualquer token válido (virar admin, renovar `exp`, etc.). |
| 7 | **Auditoria completa** | Relatório único com severidade (🔴/🟡/🟢) e placar final. |

---

## 📦 Instalação

```bash
# dependência obrigatória
pip install colorama

# opcional (só para gerar chave RSA de demonstração no módulo 4)
pip install cryptography

# rodar
python main.py
```

Requer **Python 3.8+**.

---

## 🚀 Uso

### Modo interativo (recomendado)

```bash
python main.py
```

Abre o menu. Comece pela opção **[8] Carregar / trocar token** → **[2] Usar token de demonstração** para testar tudo na hora, sem precisar de um alvo externo. O token demo é HS256 assinado com a secret `secret`, então o bruteforce o quebra em segundos.

### Modo CLI (atalhos rápidos)

```bash
# imprime um token de demonstração
python jwtforge_br.py --demo

# decodifica e inspeciona
python jwtforge_br.py --decode "eyJhbGci..."

# quebra a secret (lista embutida ou wordlist própria)
python jwtforge_br.py --crack "eyJhbGci..."
python jwtforge_br.py --crack "eyJhbGci..." --wordlist rockyou.txt

# gera variações alg:none
python jwtforge_br.py --none "eyJhbGci..."

# auditoria completa
python jwtforge_br.py --audit "eyJhbGci..."
```

---

## 🖥️ Exemplo de saída

```
┌─ HEADER (algoritmo / metadados)
  {
    "alg": "HS256",
    "typ": "JWT"
  }
  → HS256: HMAC com secret compartilhada (alvo de bruteforce)

┌─ PAYLOAD (claims / dados)
  {
    "user": "joao",
    "role": "user",
    "exp": 1782079994
  }

┌─ ANÁLISE DE TEMPO
  exp (expira em):  21/06/2026 22:13:14 → válido (faltam 1h)
```

```
  🔴 CRITICO  Secret fraca encontrada: 'secret'.
     ↳ mitigação: Usar secret aleatória ≥ 256 bits.
  🟢 OK       Expiração em janela razoável.
  🔵 INFO     Claims de autorização no token: role.

  Placar: 1 crítico(s) · 0 médio(s) · 2 ok/info
```

---

## 📱 Instalação no Termux (Android)

```bash
pkg update && pkg upgrade -y
pkg install python -y
pip install colorama
# opcional:
pip install cryptography
python jwtforge_br.py
```

---

## 🛡️ Como se defender (boas práticas JWT)

- **Nunca** aceite `alg=none`; use uma **allowlist** de algoritmos.
- Não deixe o token escolher o método de verificação (evita a confusão RS256→HS256).
- Use uma secret **longa e aleatória** (≥ 256 bits), nunca palavra de dicionário, e fora do código-fonte.
- Trate `kid`, `jku` e `x5u` como **dados não-confiáveis**: nada de concatenar em path ou SQL; use consultas parametrizadas.
- Defina expiração curta (`exp`) e valide as permissões **no servidor** — nunca confie cegamente nos claims.

---

## 📄 Licença

Distribuído sob a licença **MIT**.

## 👤 Créditos

Desenvolvido por **Kalyel** · [github.com/Kalyel473](https://github.com/Kalyel473)