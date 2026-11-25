# AgendaVet

API de agendamentos veterinários para o Projeto Prático 1 de Engenharia de Software II, Partes 1 e 2.

Dupla- Davi Dutra Satler e Willian Valadares Ramos Júnior.
Professor: Ryan Freitas

https://github.com/DaviSatler/AgendaVet-Entrega-EngSoftwareII


## Executar

Clone este repositório ou extraia o ZIP do projeto. Use Node.js 20 ou superior.

```sh
npm ci
npm run dev:parte1
npm test
npm run test:coverage
npm run dev
```

- `dev:parte1` demonstra uma consulta de R$ 120,00, uma vacina com Plano Pet+ de R$ 64,00 e um banho e tosa de R$ 65,00.
- `dev` inicia a API da Parte 2 em `http://localhost:3000`. Use `npm run build` seguido de `npm start` para executar o JavaScript compilado.
- `node scripts/validar-local.cjs` reinstala as dependências pelo lockfile, roda a demonstração, verifica o núcleo, compila e executa o Jest com cobertura. Acrescente `--docker` quando houver Docker em execução.

## API REST

| Método | Recurso | Sucesso | Erros previstos |
|---|---|---|---|
| POST | `/consultas` | 201 · consulta criada | 400 · falhas de validação em `erros` |
| GET | `/consultas` | 200 · lista | |
| GET | `/consultas/:id` | 200 · consulta | 400 · ID inválido, 404 · consulta inexistente |
| DELETE | `/consultas/:id` | 200 · consulta cancelada com `taxaCancelamento` | 400 · ID inválido, 404 · consulta inexistente, 409 · já cancelada |

Exemplo de corpo para `POST /consultas`. Use uma data futura.

```json
{
  "tutor": "Ana",
  "telefone": "31999990000",
  "pet": "Thor",
  "tipo": "CONSULTA",
  "dataHora": "2030-01-10T14:00:00-03:00",
  "planoPetMais": true
}
```

A API exige tutor com pelo menos três caracteres, telefone com 10 ou 11 dígitos, nome do pet, tipo cadastrado e data ISO válida no futuro. O plano é booleano e tem valor padrão `false`. A API ignora campos extras, inclusive `valor` e `status`. Os dados são armazenados em memória e o Adapter usa uma SDK que simula notificações no console.

| Antecedência de cancelamento | Taxa sobre o valor da consulta |
|---|---|
| 24 horas ou mais | 0% |
| De 2 horas até menos de 24 horas | 30% |
| Mais de 0 e menos de 2 horas | 50% |
| No horário da consulta ou depois | 100% |

O Plano Pet+ dá 20% de desconto no preço do atendimento e reduz à metade a taxa calculada sobre o valor agendado. Por exemplo, uma consulta Pet+ custa R$ 96,00. Se cancelada uma hora antes, a taxa é de R$ 24,00.

## Arquitetura

- `domain/` define os tipos de atendimento e os contratos de consulta.
- `pricing/` registra regras polimórficas para consulta, vacina, cirurgia e banho e tosa. `DescontoPlanoPet` decora qualquer regra. `CalculadoraDeTaxaCancelamento` aplica as faixas de antecedência.
- `repositories/` separa o contrato de persistência da implementação em memória, que usa `Map` e um contador de IDs.
- `notifications/` adapta o contrato `INotificador` à SDK legada, traduzindo a chamada e acrescentando o DDI 55.
- `facade/` concentra a orquestração de agendamento e cancelamento. `montagem.ts` instancia e injeta as dependências.
- `app.ts` cria o app Express sem abrir uma porta. `index.ts` abre a porta 3000. `legado/` preserva o código original para comparação.

Para incluir `BANHO_TOSA`, os pontos de extensão são `TipoAtendimento.ts`, `regras.ts` e `montagem.ts`. A calculadora seleciona qualquer regra registrada pelo tipo.

## Docker

Com o Docker em execução, rode os comandos abaixo na pasta do projeto.

```sh
docker build -t agendavet .
docker run --rm -p 3000:3000 agendavet
```

Consulte a API em outro terminal.

```sh
curl http://localhost:3000/consultas
```

Um container recém-iniciado responde com HTTP 200 e a lista vazia `[]`. A imagem usa `node:20-alpine`, instala pelo lockfile, compila TypeScript e executa `dist/index.js`.

## Verificação 

| Verificação | Resultado |
|---|---|
| Instalação com `npm ci` e compilação TypeScript | Aprovadas |
| Demonstração e verificação independente do núcleo | 3 agendamentos e 34 verificações aprovadas |
| Jest | 4 suítes, 49 testes aprovados |
| Testes por grupo | 12 de taxa, 10 de Facade, 21 de API e 6 de preço |
| Docker Engine 29.5.2 em máquina virtual Alpine Linux | Imagem construída e container executado na porta 3000. `GET /consultas` retornou HTTP 200 com `[]`. |

| Cobertura Jest | Instruções | Ramificações | Funções | Linhas |
|---|---:|---:|---:|---:|
| `src/pricing/` | 100% | 100% (16/16) | 100% | 100% |
| `src/facade/` | 100% | 100% (11/11) | 100% | 100% |

O Jest exige no mínimo 80% de ramificações em cada pasta. O relatório HTML é gerado em `coverage/lcov-report/index.html`. Em uma medição separada de `app.ts`, o teste de JSON malformado cobriu a resposta HTTP 400 do middleware e a cobertura de ramificações da API passou de 89,74% para 92,30%. Outro teste cobre o cancelamento com taxa de uma consulta Pet+ pela API.

Os registros da execução estão em [evidencias/](evidencias/). O [teste HTTP do container](evidencias/docker-http.txt) também verificou criação, consulta por ID, cancelamento com taxa e rejeição do cancelamento repetido. A [cópia do relatório HTML](evidencias/cobertura-html/index.html) acompanha o pacote.
