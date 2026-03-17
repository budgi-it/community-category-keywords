# community-category-keywords 🇧🇷

**O primeiro dataset open source de keywords para categorização automática de transações bancárias no Brasil.**

Mapeamento de termos em extratos (iFood, Nubank, PIX, etc.) para categorias padrão. Compatível com Open Finance, extratos de conta e cartão de crédito.

---

## Por que existe?

Não existe dataset pronto em PT-BR para isso. Os que existem são focados em EUA/UK. Este repositório nasce para preencher essa lacuna e crescer com a comunidade.

---

## Estrutura

O arquivo `category-keywords.json` contém:

| Campo | Descrição |
|------|-----------|
| `version` | Versão semântica |
| `license` | MIT |
| `description` | Descrição do dataset |
| `expense` | Mapeamentos para despesas (29 categorias) |
| `income` | Mapeamentos para receitas (11 categorias) |

Cada mapeamento: `keywords` (termos no extrato) → `categoryTerms` (nomes das categorias em PT/EN/ES).

---

## Como usar

### Baixar

```bash
curl -O https://raw.githubusercontent.com/budgi-it/community-category-keywords/main/category-keywords.json
```

### Lógica

1. Normalize a descrição: `toLowerCase().trim()`
2. Para cada mapeamento (na ordem): se alguma keyword estiver na descrição, procure uma categoria do usuário que contenha algum `categoryTerm`
3. Primeiro match vence
4. Se nenhum: use categoria padrão

### Exemplo (JavaScript)

```javascript
const data = await fetch('category-keywords.json').then(r => r.json());
const mappings = type === 'EXPENSE' ? data.expense : data.income;

function suggestCategory(transactionName, userCategories, type) {
  const normalized = transactionName.toLowerCase().trim();
  
  for (const mapping of mappings) {
    const hasKeyword = mapping.keywords.some(kw => normalized.includes(kw));
    if (!hasKeyword) continue;

    const category = userCategories.find(cat => 
      mapping.categoryTerms.some(term => cat.name.toLowerCase().includes(term))
    );
    if (category) return category;
  }
  return null;
}
```

---

## Como contribuir

1. Fork o repositório
2. Edite `category-keywords.json` — adicione keywords ou novos mapeamentos
3. Mantenha ordem: keywords mais específicas antes das genéricas (ex: "uber eats" antes de "uber")
4. Abra um Pull Request

### Regras

- Keywords em minúsculo
- Inclua variações: com/sem acento, PT/EN/ES
- `categoryTerms` = nomes das categorias padrão do app

---

## Categorias incluídas

**Despesas:** Alimentação, Transporte, Combustível, Assinatura, Supermercado, Compras, Seguro, Saúde, Academia, Lazer, Moradia, Veículo, Educação, Viagem, Cartões, Imposto, Taxas, Empréstimo, Presente, Dízimo, Doação, Salão, Pet, Vestuário, Móveis, Construção, Pagamento, Outros

**Receitas:** Salário, Vendas, Rendimento, Cashback, Investimento, Empréstimo, Ajuda, Presente, Criptomoeda, Prêmio, Outros

---

## Licença

MIT
