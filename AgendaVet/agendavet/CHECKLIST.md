# AgendaVet e os requisitos dos roteiros

## Parte 1

| Requisito | Evidência disponível |
|---|---|
| Código legado para comparação | `src/legado/sistema.ts` e `src/libs/zap-legacy-sdk.ts` |
| Domínio, contratos e injeção de dependências | `src/domain/`, `src/repositories/`, `src/notifications/`, `src/facade/` e `src/montagem.ts` |
| Regra `BANHO_TOSA` a R$ 65,00 | Registro na composição, demonstração e testes de preço e API |
| Adapter, Decorator e Facade | `ZapAdapter`, `DescontoPlanoPet` e `ClinicaFacade` |
| Três demonstrações, incluindo Pet+ e `BANHO_TOSA` | `npm run dev:parte1` na versão final com API |
| README | Identifica a dupla e explica instalação, execução e arquitetura |

## Parte 2

| Requisito | Evidência disponível |
|---|---|
| Testes de taxa com valores-limite | `src/pricing/CalculadoraDeTaxaCancelamento.test.ts` |
| Facade com mock de notificação | `src/facade/ClinicaFacade.test.ts` |
| API REST com validação e testes de caixa preta | `src/app.ts` e `tests/api.test.ts` |
| Cobertura ≥ 80% em `pricing/` e `facade/` | Medição e resumo no README |
| Docker e API na porta 3000 | `Dockerfile`, instruções e registros de validação |

## Comprovação da dupla em laboratório

- Parte 1. GitHub com acesso para o professor, branches `feature/dominio-solid` e `feature/padroes`, pelo menos seis commits semânticos dos dois alunos, PRs com revisão cruzada e comentário real, merge em `main` e histórico da extensão `BANHO_TOSA`. A folha de bordo inclui o Tribunal do Código e as perguntas da etapa.
- Parte 2. O mesmo repositório, com a branch `feature/testes`, histórico do TDD Ping-Pong alternado entre os alunos, PR revisado, merge em `main` e tag `v1.0` publicada. A folha de bordo inclui as perguntas de reflexão.
- Nas duas partes. Requisito-surpresa fornecido pelo professor, folhas de bordo em Word conforme autorização do professor, checkpoints vistados e defesa oral com alteração ao vivo.

O pacote registra execução técnica assistida. O histórico, as revisões e os registros presenciais dependem da realização e comprovação pela dupla segundo as regras da disciplina.
