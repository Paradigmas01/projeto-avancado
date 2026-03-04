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





    ### Artur

    Relatório de Funcionamento dos Testes: LoginPage (Versão Jest)
A abordagem principal desta suíte de testes é tratar o componente LoginPage como uma "caixa preta". Nós não testamos a API real ou as rotas do navegador; em vez disso, simulamos todas as dependências externas para garantir que a interface reaja corretamente a diferentes cenários.

### 1. O Mecanismo de Isolamento (Mocks)
Para que o teste rode rápido e sem erros de dependências ausentes, usamos as funções globais do Jest para criar "dublês" (mocks) das partes complexas do sistema:

jest.mock('react-i18next'): Evita que o componente quebre por não encontrar o provedor de idiomas. O mock simplesmente retorna as chaves de tradução ou o texto padrão.

jest.mock('react-router-dom'): Intercepta a função useNavigate. Em vez de mudar a URL real (o que daria erro no ambiente de teste), ele aciona a função vazia mockNavigate, permitindo que o teste verifique se ela foi chamada com a rota certa.

Hooks de API (useLogin e useSuap): Substituímos a lógica real de chamadas HTTP por funções controladas (mockMutate). Isso nos permite forçar um cenário de sucesso ou de falha instantaneamente durante os testes.

### 2. Como os Cenários São Avaliados
Utilizamos a biblioteca @testing-library/react para simular as ações de um usuário real:

Renderização Inicial: A função render(<LoginPage />) monta o componente na memória. O screen.getByPlaceholderText procura os campos de texto como um usuário faria (lendo o placeholder), garantindo que a tela carregou.

Digitação (Data Binding): O fireEvent.change simula o teclado do usuário. O teste então verifica se o valor interno do input foi atualizado em tempo real.

Validação de Formulário: O fireEvent.click aperta o botão de login com os campos vazios. O waitFor diz ao Jest para esperar até que as mensagens de "Usuário é obrigatório" apareçam na tela, confirmando que a requisição para a API foi bloqueada.

Simulação de Falha na API: Nós programamos o mockMutate para devolver um erro (Credenciais inválidas). O teste preenche os dados, clica em acessar e verifica se o alerta de erro vermelho apareceu na interface.

Simulação de Sucesso e Redirecionamento: Nós programamos o mockMutate para devolver um token falso (fake-token). Após o clique, o teste verifica se a mensagem "Login realizado com sucesso!" apareceu.

### 3. Controle de Tempo (Timers)
No código original, após o login, há um setTimeout de 1 segundo antes de redirecionar a página. Para evitar que o teste fique esperando 1 segundo real (o que deixaria a suíte lenta), usamos o jest.useFakeTimers(). Com o comando jest.advanceTimersByTime(1000), avançamos o "relógio interno" do teste em 1 segundo instantaneamente, verificando em seguida se o redirecionamento ocorreu.
