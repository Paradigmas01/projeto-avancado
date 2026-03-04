# Equipe Paradigmas 03/03/2026

# Documentação solicitada pelo Professor

## Mateus 

Esta documentação detalha as correções aplicadas ao repositório divulgaif-front para viabilizar a execução de testes unitários em um ambiente React que utiliza dependências em formato ES Modules (ESM).

### 1. Contexto do Ambiente

O projeto utiliza react-scripts (Create React App), que possui uma configuração prévia do Jest. No entanto, a utilização de bibliotecas modernas que distribuem código apenas em ESM causou erros de sintaxe (SyntaxError: Cannot use import statement outside a module) durante a execução dos testes.
### 2. Correções Aplicadas na Configuração (package.json)

Foram adicionadas configurações específicas no bloco "jest" do ficheiro package.json para lidar com a transformação de módulos e mapeamento de dependências.

    Transformação de Módulos (ESM):
    Por padrão, o Jest não transforma ficheiros dentro de node_modules. Para corrigir erros de importação em bibliotecas como @uidotdev/usehooks e axios, a propriedade transformIgnorePatterns foi atualizada para abrir exceções a esses pacotes.

        Padrão: "node_modules/(?!(axios|@uidotdev/usehooks)/)".

    Mapeamento de Módulos (Module Name Mapper):
    Para garantir que o Jest utilize a versão compatível com CommonJS do axios, foi configurado um mapeamento direto para o ficheiro .cjs da biblioteca.

        Mapeamento: "^axios$": "axios/dist/node/axios.cjs".

### 3. Infraestrutura de Suporte aos Testes

    Matchers Globais: O ficheiro src/app/setupTests.js foi configurado para importar @testing-library/jest-dom, permitindo o uso de asserções como .toBeInTheDocument() e .toHaveClass() em todos os testes.

    Limpeza de Cache: Foi executado o comando npm test -- --clearCache para garantir que as alterações estruturais no package.json fossem reconhecidas pelo Jest sem interferência de transformações antigas.

### 4. Resolução de Erros de Implementação

    Atualização de Testes Obsoletos: O teste inicial em src/app/App.test.js foi identificado como falho porque tentava encontrar o texto "learn react" (padrão do boilerplate), enquanto a aplicação renderiza "DivulgaIF". O teste foi atualizado para refletir o conteúdo real da interface.

    Tratamento de Propriedades Inválidas: Identificou-se um erro de consola onde a propriedade pageSize estava a ser passada para um elemento DOM nativo (<button>). A recomendação aplicada foi consumir essa prop no componente de paginação em vez de a propagar para o HTML.

    Compatibilidade com React Router v7: Foram identificados avisos de depreciação do react-router-dom. A solução documentada envolve a ativação das future flags (v7_startTransition e v7_relativeSplatPath) na configuração do Router.

### 5. Como Executar os Testes

Para rodar a suite de testes atualizada, utilize os comandos:

    npm test (para modo watch).

    npm test -- --watchAll=false (para execução única, ideal para CI/CD).

## Artur

Relatório de Funcionamento dos Testes: LoginPage (Versão Jest)
A abordagem principal desta suíte de testes é tratar o componente LoginPage como uma "caixa preta". Nós não testamos a API real ou as rotas do navegador; em vez disso, simulamos todas as dependências externas para garantir que a interface reaja corretamente a diferentes cenários.

### 1. O Mecanismo de Isolamento (Mocks)
    Para que o teste rode rápido e sem erros de dependências ausentes, usamos as funções globais do Jest para criar "dublês" (mocks) das partes complexas do sistema:

    jest.mock('react-i18next'): Evita que o componente quebre por não encontrar o provedor de idiomas. O mock simplesmente retorna as chaves de tradução ou o texto padrão.

    jest.mock('react-router-dom'): Intercepta a função useNavigate. Em vez de mudar a URL real (o que daria erro no ambiente de teste), ele aciona a função vazia mockNavigate, permitindo que o teste verifique se ela foi c    hamada com a rota certa.

    Hooks de API (useLogin e useSuap): Substituímos a lógica real de chamadas HTTP por funções controladas (mockMutate). Isso nos permite forçar um cenário de sucesso ou de falha instantaneamente durante os testes.

### 2. Como os Cenários São Avaliados
    Utilizamos a biblioteca @testing-library/react para simular as ações de um usuário real:

    Renderização Inicial: A função render(<LoginPage />) monta o componente na memória. O screen.getByPlaceholderText procura os campos de texto como um usuário faria (lendo o placeholder), garantindo que a tela carregou.

    Digitação (Data Binding): O fireEvent.change simula o teclado do usuário. O teste então verifica se o valor interno do input foi atualizado em tempo real.

    Validação de Formulário: O fireEvent.click aperta o botão de login com os campos vazios. O waitFor diz ao Jest para esperar até que as mensagens de "Usuário é obrigatório" apareçam na tela, confirmando que a     requisição para a API foi bloqueada.

    Simulação de Falha na API: Nós programamos o mockMutate para devolver um erro (Credenciais inválidas). O teste preenche os dados, clica em acessar e verifica se o alerta de erro vermelho apareceu na interface.

    Simulação de Sucesso e Redirecionamento: Nós programamos o mockMutate para devolver um token falso (fake-token). Após o clique, o teste verifica se a mensagem "Login realizado com sucesso!" apareceu.

### 3. Controle de Tempo (Timers)
    No código original, após o login, há um setTimeout de 1 segundo antes de redirecionar a página. Para evitar que o teste fique esperando 1 segundo real (o que deixaria a suíte lenta), usamos o jest.useFakeTimers(). Com     o comando jest.advanceTimersByTime(1000), avançamos o "relógio interno" do teste em 1 segundo instantaneamente, verificando em seguida se o redirecionamento ocorreu.


## Vinicius e Pedro 
Neste trabalho foram desenvolvidos testes unitários para o componente **MyWorks**, localizado no arquivo `page.jsx`. Para isso foram utilizados o **Jest** e a **React Testing Library**, que permitem testar componentes React simulando o comportamento do usuário.

Foi criado o arquivo `newWorks.test.js`, onde foram implementados testes para verificar se o componente renderiza corretamente e se suas principais funcionalidades estão funcionando. Como o componente depende de vários módulos externos, foram utilizados **mocks** para simular serviços como tradução, navegação, hooks de dados e componentes da interface. Isso permite que os testes sejam executados de forma isolada, sem depender de APIs ou de outras partes do sistema.

Entre os testes realizados estão a verificação da renderização do título e dos botões da página, a abertura do modal de filtros, a navegação ao clicar no botão **Novo Trabalho**, a alteração no campo de busca e a renderização do componente de resultados paginados.

De forma geral, os testes garantem que as funcionalidades principais da página **MyWorks** continuem funcionando corretamente, contribuindo para a qualidade e manutenção do sistema.

## Eduardo

## Relatório de Funcionamento dos Testes: MyWorks

A suíte trata o componente `MyWorks` como uma "caixa preta". Todas as dependências externas são simuladas (mockadas) para que a interface seja testada de forma isolada, sem chamadas reais à API ou ao navegador.

### 1. O Mecanismo de Isolamento (Mocks)

**`useMyWorks`**: Substitui as chamadas à API, permitindo simular listas de trabalhos, lista vazia ou estado de carregamento instantaneamente.

**`jotai`**: Preserva a função `atom()` real e mocka apenas o `useAtom`, permitindo controlar os valores de página, tamanho e busca em cada teste.

**`react-router-dom`**: Preserva os exports reais do módulo e intercepta apenas o `useNavigate`, verificando se a navegação foi chamada com a rota correta sem mudar a URL real.

**`react-i18next`**: Fornece um dicionário de traduções fixo, tornando os textos da interface previsíveis nos testes.

**`FiltrarBuscaModal`**: Substituído por um componente simplificado para testar apenas se o modal abre e fecha, sem depender de sua implementação interna.

### 2. Cenários Avaliados

**Renderização inicial**: Verifica se o título, os botões e o campo de busca aparecem corretamente ao carregar a página.

**Estado populado e vazio**: Verifica se a lista de trabalhos é exibida quando há dados, e se a mensagem "Nenhum trabalho encontrado." aparece quando não há.

**Clique em botões**: Verifica se "Novo Trabalho" navega para a rota correta e se "Filtrar Trabalhos" abre o modal.

**Busca**: Verifica se digitar um novo valor atualiza o estado e reseta a página, e se digitar o mesmo valor não reseta a página.

**Estado de carregamento**: Verifica se "Carregando..." é exibido enquanto os dados estão sendo buscados.

### 3. Problemas Encontrados

**`useAtom` identificado via `.toString()`**: Atoms do Jotai são objetos — `.toString()` nunca retorna strings como `'page'` ou `'size'`. Corrigido comparando diretamente com as referências importadas: `if (atom === pageAtom)`.

**`jest.mock('jotai')` removendo o `atom`**: Mockar o módulo inteiro apagava a função `atom()`, quebrando o `useMyWorks.js` na inicialização. Corrigido com `jest.requireActual('jotai')`, preservando os exports reais.

**`jest.mock('react-router-dom')` sem `requireActual`**: Removia exports como `<BrowserRouter>` usados por componentes filhos. Corrigido da mesma forma que o Jotai.

**Chave `common.loading` ausente**: Sem essa chave no mock de traduções, o componente exibia `"common.loading"` como texto literal. Corrigido adicionando `'common.loading': 'Carregando...'` ao dicionário.

**Caminhos relativos incorretos**: Os mocks de `utils` e `FiltrarBuscaModal` tinham um `../` a menos, pois o arquivo está dentro de `__tests__/`. Corrigido adicionando mais um nível nos caminhos.

**Lógica incorreta no teste de busca com valor igual**: O teste verificava que `mockSetSearch` era chamado, mas o `fireEvent.change` para o mesmo valor não disparava o `onChange`. A linha foi removida — o objetivo do teste é apenas garantir que `setCurrentPage` não seja chamado.

### 4. Resultado Final

Após todas as correções, os 8 testes passam com sucesso, cobrindo os principais fluxos de interface da página "Meus Trabalhos".

---
