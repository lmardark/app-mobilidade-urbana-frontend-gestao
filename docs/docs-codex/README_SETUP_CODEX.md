<!-- CODEX: 122 linhas criadas neste arquivo; guia de setup criado integralmente pelo CODEX para a equipe e para o Claude. -->

# Setup do painel de gestão

Painel web Quasar/Vue usado para administrar usuários, motoristas, documentos, veículos, tarifas, produtos e corridas.

## Requisitos

- Node.js `22.12+`, 24, 26 ou 28, conforme `package.json`.
- npm 6.13.4 ou superior.
- Backend Laravel iniciado e com banco preparado.
- Navegador moderno.

Node.js 24 e npm 11 foram usados nas validações atuais dos outros frontends.

## 1. Instalar dependências

```bash
npm ci
```

O `postinstall` executa `quasar prepare` automaticamente.

## 2. Configurar a API

O painel lê `API_URL` do arquivo `.env` na raiz:

```dotenv
API_URL=http://localhost/api
```

Se o backend estiver em outra porta:

```dotenv
API_URL=http://localhost:8000/api
```

Se o painel for aberto por outro computador na rede, use o IPv4 da máquina do backend. Não coloque tokens ou segredos nesse arquivo; variáveis usadas pelo frontend são entregues ao navegador.

## 3. Preparar o backend

No repositório Laravel:

```bash
docker compose up -d
docker compose exec laravel.test php artisan migrate
docker compose exec laravel.test php artisan db:seed --class=CenarioCorridaDesenvolvimentoSeeder
```

O painel usa autenticação JWT e envia o token salvo no `localStorage` em cada chamada Axios.

## 4. Iniciar em desenvolvimento

```bash
npm run dev
```

O Quasar abre o navegador automaticamente em `http://localhost:8080`.

O painel usa a porta `8080`; por isso o guia do backend publica o Reverb na porta externa `6001`.

## 5. Login local

O seeder de desenvolvimento cria:

| Perfil     | E-mail                          | Senha             |
| ---------- | ------------------------------- | ----------------- |
| Motorista  | `motorista.codex@example.test`  | `desenvolvimento` |
| Passageiro | `passageiro.codex@example.test` | `desenvolvimento` |

Durante o desenvolvimento, qualquer usuário autenticado pode acessar o endpoint de aprovação de documentos, conforme a regra temporária definida para o projeto. Antes de produção, crie perfis administrativos e restrinja essas rotas.

## 6. Fluxo de aprovação do motorista

1. Entre no painel.
2. Abra a lista de motoristas.
3. Selecione o cadastro e visualize os documentos.
4. Aprove ou reprove cada documento.
5. Confirme a situação recalculada do motorista.
6. Verifique CNH, veículo e os quatro documentos obrigatórios antes de considerar o cadastro completo.

No ambiente de desenvolvimento, dados fictícios podem ser usados para CNH, veículo e documentos.

## 7. Verificações

```bash
npm run lint
npm run test
npm run build
```

O script `test` atual apenas informa que não existem testes automatizados. O build e o lint são as validações efetivas deste repositório.

O resultado de produção é gravado em `dist/`:

```bash
npm run build
```

## 8. Ordem de inicialização integrada

1. Backend, MySQL e Valkey.
2. Migrações e seeder de desenvolvimento.
3. Worker da fila e Reverb.
4. Painel Quasar.
5. Aplicativos do passageiro e motorista.

## 9. Solução de problemas

- **Erro 401:** limpe o token do navegador, entre novamente e confira o `JWT_SECRET` do backend.
- **Network Error:** abra a URL da API no navegador e confira `API_URL`, porta e CORS.
- **Tela sem dados:** confira as migrações, seeders e a resposta da aba Network do navegador.
- **Porta 8080 ocupada:** encerre o processo conflitante; use `6001` como porta externa do Reverb.
- **Variável antiga:** pare e reinicie `npm run dev` após editar `.env`.
- **Dependências inconsistentes:** remova apenas `node_modules`, preserve `package-lock.json` e execute `npm ci`.

## Segurança

- Não registre JWT, senhas ou respostas completas de autenticação no console.
- Não armazene segredos em variáveis expostas ao navegador.
- Restrinja aprovação de documentos a perfis administrativos antes de produção.
- Use HTTPS e cookies ou armazenamento com proteções adequadas no ambiente publicado.
