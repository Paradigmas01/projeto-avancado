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
