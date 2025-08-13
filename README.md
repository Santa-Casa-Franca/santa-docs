# 📚 Santa Docs - Documentação Técnica

Bem-vindo ao repositório **Santa Docs**, nosso hub central de documentação técnica da empresa. Este repositório contém toda a documentação gerada com **MkDocs**, incluindo guias de instalação, manuais de sistemas e referências de banco de dados.

---

## 🔹 Sumário
- [Sobre o Projeto](#sobre-o-projeto)
- [Pré-requisitos](#pré-requisitos)
- [Instalação](#instalação)
- [Execução](#execução)
- [Acesso à Documentação](#acesso-à-documentação)
- [Contribuição](#contribuição)
- [Licença](#licença)

---

## 📝 Sobre o Projeto

O MkDocs é utilizado para gerar uma documentação clara, organizada e navegável, com suporte a:

- Temas customizáveis (usamos o **Material for MkDocs**)  
- Navegação hierárquica  
- Integração com o [SchemaSpy](./schemaspy/index.html) para documentação do banco de dados  
- Geração de PDF e HTML estático  

---

## ⚙️ Pré-requisitos

Antes de rodar localmente:

- [Python 3.10+](https://www.python.org/downloads/)  

---

## 🚀 Instalação

Clone o repositório:

```bash
git clone https://github.com/Santa-Casa-Franca/santa-docs.git
cd santa-docs
```

Instale dependências:

```bash
pip install -r requirements.txt
```

---

## ▶️ Execução

Para rodar o servidor local e visualizar a documentação:

```bash
mkdocs serve -a 0.0.0.0:8080
```

Acesse em seu navegador:

```
http://127.0.0.1:8000/
```

Para gerar a versão estática (HTML) pronta para deploy:

```bash
mkdocs build
```

Para deploy direto no GitHub Pages:

```bash
mkdocs gh-deploy
```