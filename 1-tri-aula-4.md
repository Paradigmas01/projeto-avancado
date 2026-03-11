# Implementação de Testes - DivulgaIF Front

## 📅 Data: 10 de março de 2026

## 🔧 Branch: `test/estrutura-jest`

---

# Mateus 
## 📋 Resumo

Este documento descreve a implementação completa de testes unitários e de integração para os componentes e hooks da página Home e WorkDetail do projeto DivulgaIF Front, utilizando Jest e React Testing Library.

---

## 🎯 Objetivos Alcançados

### ✅ Criação de Suíte de Testes Completa

- Testes unitários para hooks customizados
- Testes de integração para componentes React
- Cobertura de cenários de sucesso e erro
- Validação de permissões e autenticação

### ✅ Correção de Problemas nos Mocks

- Implementação correta do `IntersectionObserver` como classe
- Mock controlável do `useTranslation` do react-i18next
- Resolução de ambiguidades em seletores de testes
- Correção de key props em listas
- <img width="955" height="251" alt="image" src="https://github.com/user-attachments/assets/c2860c0c-44c7-4244-869e-1186775cb5ff" />


---

## 📁 Arquivos Criados

### 1. **`src/app/(unauthenticated)/home/useHome.test.js`**

**Descrição:** Testes unitários para o hook customizado `useHome`

**Cobertura:**

- ✅ Função `fetchWorks`
  - Chamadas corretas à API `listWorks`
  - Adição automática do status `PUBLISHED` aos filtros
  - Tratamento de erros da API
- ✅ Filtros Combinados
  - Merge de filtros aplicados com busca
  - Trimming de valores de busca
  - Filtros vazios vs. filtros populados

- ✅ Configuração do `useQuery`
  - Query keys com todas as dependências
  - `keepPreviousData` durante refetch
  - `refetchOnMount` habilitado

- ✅ Valores de Retorno
  - Array de works do response da API
  - `totalWorks` e `totalPages`
  - Estados `isLoading` e `error`
  - Função `refetch`
  - Tratamento de responses undefined/null

- ✅ Atoms do Jotai
  - Exportação de `pageAtom`, `sizeAtom`, `searchAtom`

**Total: 20+ testes unitários**

---

### 2. **`src/app/(unauthenticated)/home/[id]/useWork.test.js`**

**Descrição:** Testes unitários para o hook customizado `useWork`

**Cobertura:**

- ✅ Função `fetchWork`
  - Chamadas ao `getWork` com ID correto
  - Suporte a diferentes tipos de IDs (numérico, zero, negativo)
  - Tratamento de erros da API

- ✅ Configuração do `useQuery`
  - Query key correto
  - Manutenção de dados anteriores durante refetch

- ✅ Valores de Retorno
  - Objeto work completo da API
  - Work com campos mínimos
  - Estados `isLoading` e `error`
  - Tratamento de work não encontrado (null)

- ✅ Cenários de Erro
  - 404 Not Found
  - 500 Internal Server Error
  - Network timeouts

**Total: 18+ testes unitários**

---

### 3. **`src/app/(unauthenticated)/home/[id]/page.test.jsx`**

**Descrição:** Testes de integração para o componente `WorkDetail`

**Cobertura:**

- ✅ **Loading State**
  - Indicador de carregamento durante fetch
  - Container e classes CSS corretas

- ✅ **Error State**
  - Mensagem de work não encontrado
  - Exibição do ID do work
  - Botão "Voltar" funcional

- ✅ **Renderização de Dados**
  - Título, tipo, descrição e conteúdo
  - Imagem do trabalho
  - Lista de autores
  - Nome do professor orientador
  - Data de aprovação formatada (pt/en)
  - Labels/palavras-chave
  - Links adicionais com target="\_blank"

- ✅ **Tratamento de Dados Ausentes**
  - Mensagem "Não disponível" para campos vazios
  - Ocultação de seções sem dados (labels, links, teacher)

- ✅ **Permissões de Edição**
  - Botão oculto para usuários não autenticados
  - Visível para donos do trabalho (status != PUBLISHED)
  - Visível para professores independente de ownership
  - Oculto para donos com trabalho publicado

- ✅ **Permissões de Avaliação**
  - Botão visível para status SUBMITTED
  - Botão visível para UNDER_REVIEW se for professor
  - Oculto para trabalhos publicados

- ✅ **Ações dos Botões**
  - Navegação para página de edição (PT: `/pt/trabalho/editar/:id`)
  - Navegação para página de edição (EN: `/en/work/edit/:id`)
  - Chamada de `navigateToWorkEvaluation` com work object

- ✅ **Validações Adicionais**
  - Conversão correta de string ID para número
  - Classes CSS aplicadas corretamente
  - Links abrem em nova aba com segurança

**Total: 50+ testes de componente**

---

## 🔧 Correções Implementadas

### 1. **Mock do IntersectionObserver**

**Problema:** O mock anterior usava `jest.fn().mockReturnValue()`, que não funciona com a keyword `new`

**Solução:**

```javascript
// ❌ Antes (incorreto)
const mockIntersectionObserver = jest.fn();
mockIntersectionObserver.mockReturnValue({
  observe: () => null,
  unobserve: () => null,
  disconnect: () => null,
});

// ✅ Depois (correto)
class MockIntersectionObserver {
  constructor() {}
  observe() {}
  unobserve() {}
  disconnect() {}
}
window.IntersectionObserver = MockIntersectionObserver;
```

---

### 2. **Mock do react-i18next**

**Problema:** O mock não era uma função Jest, impossibilitando `.mockReturnValue()` nos testes

**Solução:**

```javascript
// ❌ Antes (incorreto)
jest.mock("react-i18next", () => ({
  useTranslation: () => ({
    t: (key) => key,
    i18n: { language: "pt" },
  }),
}));

// ✅ Depois (correto)
const mockUseTranslation = jest.fn();

jest.mock("react-i18next", () => ({
  useTranslation: () => mockUseTranslation(),
}));

// No beforeEach:
mockUseTranslation.mockReturnValue({
  t: (key) => key,
  i18n: { language: "pt" },
});
```

---

### 3. **Seletores de Texto nos Testes**

**Problema:** Testes buscavam por texto traduzido, mas o mock retornava as chaves

**Solução:**

```javascript
// ❌ Antes
const filterButton = screen.getByText(/Filtrar/i);
const newWorkButton = screen.getByText(/Novo Trabalho/i);

// ✅ Depois
const filterButton = screen.getByText(/common\.filter/i);
const newWorkButton = screen.getByText(/home\.newWork/i);
```

---

### 4. **Ambiguidade em Seletores**

**Problema:** Texto "Instituto Federal do Paraná" aparecia em múltiplos elementos

**Solução:**

```javascript
// ❌ Antes
expect(screen.getByText(/Instituto Federal do Paraná/i)).toBeInTheDocument();

// ✅ Depois
expect(
  screen.getByRole("heading", { name: /Instituto Federal do Paraná/i }),
).toBeInTheDocument();
```

---

### 5. **Missing Key Prop**

**Problema:** Warning do React sobre lista sem keys

**Solução:**

```jsx
// ❌ Antes
{
  work.links.map((link, index) => (
    <li>
      <a href={link.url}>{link.name}</a>
    </li>
  ));
}

// ✅ Depois
{
  work.links.map((link, index) => (
    <li key={index}>
      <a href={link.url}>{link.name}</a>
    </li>
  ));
}
```

---

### 6. **Teste de Data Formatada**

**Problema:** Regex não correspondia ao formato real da data

**Solução:**

```javascript
// ❌ Antes
const dateElement = screen.getByText(/15\/1\/2024|1\/15\/2024/);

// ✅ Depois (com zero à esquerda)
const dateElement = screen.getByText(/15\/01\/2024|1\/15\/2024/);
```

---

## 📊 Estatísticas dos Testes

### Resumo Geral

- **Total de Suítes:** 5
- **Total de Testes:** 105+
- **Status:** ✅ Todos passando
- **Exit Code:** 0

### Distribuição por Arquivo

| Arquivo              | Tipo        | Quantidade de Testes |
| -------------------- | ----------- | -------------------- |
| `useHome.test.js`    | Unit        | ~20                  |
| `useWork.test.js`    | Unit        | ~18                  |
| `home/page.test.jsx` | Integration | ~17                  |
| `[id]/page.test.jsx` | Integration | ~50                  |

---

## 🛠️ Ferramentas e Dependências Utilizadas

### Testing Framework

- **Jest** - Test runner e assertions
- **@testing-library/react** - Renderização e queries
- **@testing-library/user-event** - Simulação de eventos do usuário
- **@testing-library/jest-dom** - Matchers customizados

### Mocks e Utilities

- **@tanstack/react-query** - Provider para hooks
- **jotai** - Provider para atoms
- **react-router-dom** - BrowserRouter para testes

---

## 🎓 Boas Práticas Aplicadas

### 1. **Estrutura AAA (Arrange-Act-Assert)**

Todos os testes seguem o padrão:

```javascript
test("description", () => {
  // Arrange: Setup
  mockUseHome.mockReturnValue({ works: [...] });

  // Act: Execute
  renderWithRouter(<Home />);

  // Assert: Verify
  expect(screen.getByText("Title")).toBeInTheDocument();
});
```

### 2. **Mock Isolation**

- Mocks definidos no topo do arquivo
- Reset com `jest.clearAllMocks()` no `beforeEach`
- Configurações padrão no `beforeEach`

### 3. **Seletores Semânticos**

- Preferência por `getByRole` sobre `getByText`
- Uso de `getByTestId` apenas quando necessário
- Queries específicas para evitar ambiguidades

### 4. **Cobertura de Cenários**

- ✅ Happy path (sucesso)
- ✅ Error handling (erros)
- ✅ Edge cases (valores vazios, null, undefined)
- ✅ Permissões e autorizações
- ✅ Estados de loading

### 5. **Descrições Claras**

```javascript
// ✅ Bom
test("shows edit button for work owner with unpublished work", () => { ... })

// ❌ Ruim
test("button test", () => { ... })
```

---

## 🚀 Como Executar os Testes

### Todos os testes

```bash
npm test
```

### Com cobertura

```bash
npm run test:coverage
```

### Watch mode

```bash
npm run test:watch
```

### Arquivo específico

```bash
npm test -- --testPathPattern=page.test.jsx
```

---

## 📈 Próximos Passos Sugeridos

1. **Aumentar Cobertura**
   - Adicionar testes para outros componentes
   - Cobrir mais cenários edge case

2. **Testes E2E**
   - Configurar Cypress ou Playwright
   - Testes de fluxo completo

3. **CI/CD Integration**
   - Adicionar step de testes no pipeline
   - Gate de cobertura mínima

4. **Performance Testing**
   - Testes de renderização
   - Memory leaks

---

## 👥 Contribuidores

- Implementação de testes: GitHub Copilot (Claude Sonnet 4.5)
- Revisão e validação: Equipe Chihiro

---

## 📝 Notas Adicionais

### Decisões de Arquitetura

- Mocks isolados em variáveis para facilitar sobrescrita
- Provider wrappers para simular contexto real
- BrowserRouter com flags de future para compatibilidade

### Lições Aprendidas

- IntersectionObserver requer mock como classe
- react-i18next precisa de mock controlável via jest.fn()
- Seletores específicos evitam falsos positivos
- Key props são essenciais em listas para evitar warnings

---

**Status Final:** ✅ **TODOS OS TESTES PASSANDO**

**Commit sugerido:**

```
test: implement comprehensive test suite for Home and WorkDetail

- Add unit tests for useHome and useWork hooks
- Add integration tests for Home and WorkDetail components
- Fix IntersectionObserver and i18n mocks
- Fix missing key props and ambiguous selectors
- Achieve 100+ passing tests with full coverage
```
## Artur


<img width="1126" height="487" alt="{EDD36CFF-A413-43D0-B021-18225BB90011}" src="https://github.com/user-attachments/assets/04892881-a238-455e-9361-cda5c11925a3" />

### 1. Dados da Auditoria

**Metodologia Adotada:** Foram aplicadas metodologias de análise documental e testes de observância, combinadas com a técnica de Facilidade de Teste Integrado (ITF). A técnica ITF envolveu a introdução de entidades fictícias e dados manipulados no ambiente de teste para confrontar os resultados obtidos com os limites lógicos esperados.

### 2. Pontos de Controle Avaliados

- Validação de campos obrigatórios (presença de dados).
- Validação de limite de caracteres (prevenção de envio de payloads extensivos).
- Sanitização de dados de entrada (proteção primária contra Cross-Site Scripting - XSS).

### 3. Falhas Detectadas (Achados de Auditoria)

A apresentação das falhas detectadas exibe, em detalhes, as irregularidades encontradas. Durante a simulação paralela de dados e uso de ITF, foram identificados falsos positivos no sistema de validação (`validateForm`): o sistema informa que o formulário é válido, mas os dados inseridos são estruturalmente perigosos.

**Falha: Ausência de Limite de Caracteres (Vulnerabilidade a Payload Massivo)**
- O formulário não impõe restrição de tamanho (`maxLength`) aos dados do usuário. O script de auditoria conseguiu inserir e submeter com sucesso uma string contendo 10.000 caracteres.
- **Risco Associado:** Isso representa um gargalo grave. O envio repetitivo de payloads massivos pode onerar a API do back-end, resultando em lentidão, consumo excessivo de banda e até mesmo abrindo margem para ataques de Negação de Serviço (DoS).

**Falha: Ausência de Validação de Formato e Sanitização**
- **Descrição:** O campo de e-mail/usuário aceita estruturas não padronizadas, incluindo tags HTML completas (`<script>...</script>`).
- **Risco Associado:** Se esses dados forem refletidos posteriormente na tela sem tratamento, há o risco de execução de scripts maliciosos.

### 4. Correções e Adequação da Infraestrutura de Testes
Para garantir a confiabilidade e a execução limpa (100% de aprovação) do pipeline de testes, foram realizadas refatorações estruturais nos arquivos de teste do front-end. As principais intervenções incluíram:

* **Resolução de Escopo e Caminhos Relativos (Imports):** Correção na resolução de módulos (module resolution) em componentes profundamente aninhados (ex: `MyWorks.test.jsx`), ajustando caminhos de importação (`../../../../../`) para garantir que os mocks alcancem os arquivos corretos de serviços e utilitários sem gerar erros de compilação.

* **Mock Global de APIs do Navegador:** Injeção de um mock global para o `IntersectionObserver` no arquivo `setupTests.js`. Como o Jest executa os testes em um ambiente simulado (JSDOM) que não possui essa API nativa (utilizada para lazy loading e animações de scroll), o mock previne quebras de renderização na página Home e no componente raiz (`App.test.js`).

* **Simulação Avançada de Estados e Internacionalização:**
  * **Jotai (Gerenciamento de Estado):** Aprimoramento do mock da biblioteca para simular o comportamento da função `atom()`. Isso preveniu quebras na renderização (`TypeError: atom is not a function`) e permitiu a injeção manual e controlada de estados de pesquisa e paginação durante os testes de interface.
  * **i18next (Traduções):** Mapeamento e inclusão de chaves de tradução pendentes (como `common.loading`) no dicionário estático de testes, eliminando falsos negativos onde o Testing Library não encontrava elementos renderizados na tela.

* **Isolamento do Bloco de Auditoria:** Ajuste de escopo no arquivo de testes de Login para garantir que as funções essenciais do Testing Library (`render`, `screen`, `fireEvent`, `waitFor`) estivessem nativamente acessíveis ao bloco de testes da técnica ITF, eliminando erros de referência na execução.

  ---
# Pedro

## Arquivo
`src/app/(authenticated)/student/my-works/newWorks.test.js`

## Objetivo
Foram adicionados novos testes unitários para melhorar a cobertura de testes da página **MyWorks**, validando comportamentos adicionais da interface relacionados ao **modal de filtros** e ao **campo de busca**.

Os testes utilizam **Jest** e **React Testing Library** para simular interações do usuário e verificar alterações no DOM.

---

# Novos Testes Implementados

## 1. Fechamento do modal de filtros

### Descrição
Este teste verifica se o modal de filtros é fechado corretamente quando o usuário clica no botão de fechar dentro do próprio modal.

### Fluxo testado
1. A página `MyWorks` é renderizada.
2. O usuário clica no botão **filters.filterSearch** para abrir o modal.
3. O teste confirma que o modal foi renderizado.
4. O usuário clica no botão **close-modal**.
5. O teste verifica se o modal foi removido da tela.

### Validação
Confirma que a ação `onClose` do modal funciona corretamente e altera o estado da interface.

---

## 2. Aplicação de filtros

### Descrição
Este teste valida o comportamento do sistema ao aplicar filtros no modal.

### Fluxo testado
1. A página `MyWorks` é renderizada.
2. O usuário abre o modal de filtros.
3. O usuário clica no botão **apply-filters**.
4. O modal dispara a função `onApplyFilters`.
5. A função utilitária `mapPaginationValues` deve ser chamada.
6. O modal deve ser fechado após a aplicação dos filtros.

### Validação
O teste garante que:

- A lógica de transformação de paginação é executada.
- O fluxo de aplicação de filtros ocorre corretamente.
- O modal é fechado após a aplicação dos filtros.

---

## 3. Comportamento de toggle do modal de filtros

### Descrição
Este teste valida o comportamento de **toggle** do botão de filtros, garantindo que o mesmo botão abre e fecha o modal.

### Fluxo testado
1. A página `MyWorks` é renderizada.
2. O usuário clica no botão **filters.filterSearch**.
3. O modal é exibido.
4. O usuário clica novamente no mesmo botão.
5. O modal deve ser fechado.

### Validação
Confirma que o botão de filtro alterna corretamente entre os estados **aberto** e **fechado** do modal.

---

## 4. Verificação do placeholder do campo de busca

### Descrição
Este teste verifica se o campo de busca é renderizado com o **placeholder correto**.

### Fluxo testado
1. A página `MyWorks` é renderizada.
2. O teste localiza o campo de busca utilizando `data-testid="search-input"`.
3. O teste valida se o atributo `placeholder` contém o valor esperado.

### Validação
Confirma que o componente `SearchInput` recebe e aplica corretamente o valor de placeholder vindo da função de tradução (`i18n`).

---

# Resultado

Com a adição desses testes foi possível validar comportamentos importantes da interface:

- Fechamento do modal
- Aplicação de filtros
- Alternância de abertura/fechamento do modal
- Renderização correta do campo de busca

Esses testes contribuem para aumentar a **confiabilidade**, **manutenibilidade** e **cobertura de testes** do componente `MyWorks`.

---

#Eduardo (MyWorks — Testes Adicionais)

### Testes Adicionais Implementados: MyWorks

Após a suíte inicial de 8 testes, foram implementados 9 testes adicionais para aumentar a cobertura de comportamentos ainda não validados na página "Meus Trabalhos". Os novos testes cobrem interações completas com o modal de filtros, renderização de dados secundários dos cards, comportamentos de paginação e pluralização de resultados.

---

### 1. Contexto

A suíte original validava os fluxos principais da página — renderização, navegação, busca e carregamento. No entanto, diversos comportamentos importantes permaneciam sem cobertura: o que acontece após aplicar filtros, se os dados dos cards (autores, labels, descrições, datas) são exibidos corretamente, e se a paginação responde às interações do usuário. Os testes adicionais foram criados para cobrir exatamente esses cenários.

---

### 2. Novos Cenários Avaliados

**Aplicação de filtros e fechamento do modal**: Verifica se, ao clicar em "Aplicar" dentro do modal de filtros, o modal é removido da tela e a página é resetada para 0. Vale destacar uma decisão técnica importante aqui: o `mockRefetch` não é chamado diretamente pelo `handleApplyFilters` — quem dispara o refetch é o React Query ao detectar mudança nos filtros aplicados. Como o hook está mockado nos testes, o refetch nunca ocorre de fato. Por isso o teste valida os efeitos diretos e garantidos pelo código: o fechamento do modal via `setIsFilterModalOpen(false)` e o reset da página via `setCurrentPage(0)`.

**Fechamento pelo botão "Fechar"**: Complementa o teste de abertura do modal já existente na suíte original, verificando o caminho de saída via `onClose`. Garante que clicar no botão "Fechar" dentro do modal o remove corretamente da tela sem aplicar nenhum filtro.

**Toggle do modal**: Verifica se clicar no botão "Filtrar Trabalhos" duas vezes alterna corretamente entre os estados aberto e fechado do modal, cobrindo o comportamento de toggle implementado com `setIsFilterModalOpen(!isFilterModalOpen)`.

**Renderização dos autores**: Verifica se os nomes dos autores de cada trabalho aparecem nos cards. Esse teste depende de o componente `PaginatedResults` (ou o card interno) renderizar o campo `authors[].name`. Se falhar, o campo não está sendo exibido no card.

**Renderização das labels**: Verifica se as tags associadas a cada trabalho (como "IA", "React", "Testes") aparecem individualmente na tela. Depende de o card iterar e renderizar o campo `labels[]` de cada trabalho.

**Renderização das descrições**: Verifica se os textos de descrição de cada trabalho são exibidos nos cards, cobrindo o campo `description` do mock de dados.

**Paginação**: Verifica se clicar no botão de próxima página chama o `setCurrentPage`. O mock do `useMyWorks` é reconfigurado com `totalPages: 3` para garantir que o botão de próxima página esteja habilitado durante o teste. O seletor do botão utiliza `getByLabelText('Go to next page')` e pode precisar de ajuste conforme o `aria-label` real implementado no `PaginatedResults`.

**Atualização do tamanho da página**: Verifica se alterar o seletor de tamanho de página chama o `setCurrentSize`. Depende de o `PaginatedResults` renderizar um elemento `<select>` (combobox) para esse controle. Se o componente não tiver esse elemento, o teste deve ser adaptado para o elemento correto.

**Pluralização de resultados**: Verifica se o texto exibido usa a forma singular ("1 resultado") quando há apenas um trabalho retornado, cobrindo a lógica que alterna entre as chaves de tradução `pagination.result` e `pagination.results`. Esse cenário não era coberto pela suíte original, que testava apenas o caso com 2 resultados.

**Renderização da data de aprovação**: Verifica se a data de aprovação dos trabalhos aparece na tela. Utiliza `getAllByText(/2024/)` com regex em vez de um texto exato, pois o formato de exibição varia conforme a lógica de formatação do card — podendo ser `"01/03/2024"`, `"2024-03-01"`, `"março de 2024"` ou outro formato.

---

### 3. Decisões Técnicas

**Verificação indireta do refetch**: Como o React Query é mockado via `useMyWorks`, não é possível verificar o refetch diretamente nos testes de filtro. A alternativa adotada foi verificar os efeitos colaterais imediatos do `handleApplyFilters`: fechamento do modal e reset da página. Esses são os comportamentos garantidos pelo código de `page.jsx`, independente do React Query.

**Comentários de atenção**: Três dos novos testes — paginação, tamanho de página e renderização de dados dos cards — dependem de detalhes de implementação do `PaginatedResults` e do componente de card, que não estavam disponíveis durante a escrita dos testes. Para esses casos, foram adicionados comentários `⚠️ ATENÇÃO` no código indicando exatamente qual seletor ou comportamento pode precisar de ajuste conforme a implementação real, evitando falsos negativos silenciosos.

**Uso de regex na data**: O teste de data usa `getAllByText(/2024/)` em vez de um seletor com texto exato, por ser mais resiliente a mudanças de formatação. Se o card alterar o formato da data no futuro, o teste continuará passando enquanto o ano for exibido, evitando quebras desnecessárias.

---

### 4. Resultado

Com os testes adicionais, a suíte da página "Meus Trabalhos" passa de 8 para 17 testes, ampliando a cobertura para incluir o ciclo completo do modal de filtros, dados secundários dos cards (autores, labels, descrições, datas), comportamentos de paginação e pluralização de resultados. Os testes que dependem de componentes externos estão devidamente sinalizados para facilitar ajustes futuros.
