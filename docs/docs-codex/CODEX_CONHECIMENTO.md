<!-- CODEX: 53 linhas criadas neste arquivo; documento criado integralmente pelo CODEX para compartilhar contexto com o Claude. -->

# Conhecimento do CODEX — Painel de gestão

Este arquivo foi criado pelo **CODEX** em 17/09/2026. Ele registra o fluxo de gestão auditado no projeto Quasar.

## Papel do repositório

- Painel web Quasar/Vue para consultar usuários, motoristas e documentos.
- Consome os endpoints autenticados do backend Laravel.
- A página de motoristas abre o componente de documentos e refaz a listagem após alterações.

## Correções realizadas

- A listagem agora lê `motorista.status`, que é o campo devolvido pelo backend.
- O painel exibe corretamente os estados pendente, em análise, aprovado e reprovado.
- Após aprovar ou reprovar um documento, o componente emite um evento de atualização.
- A página recarrega os motoristas ao receber esse evento, evitando manter um status antigo na tela.

## Aprovação de documentos

- O backend possui lógica em desenvolvimento que recalcula a situação após alteração de documento.
- CNH, veículo e quatro documentos aprovados são os requisitos finais confirmados pelo usuário.
- Por enquanto, os dados continuam permissivos para facilitar os testes.
- O painel não define sozinho quem pode aprovar; essa autorização deve ser garantida no backend.

## Risco de autorização

- O modelo atual não possui papel administrativo ou permissão de gestão.
- Qualquer usuário autenticado, exceto o próprio motorista do documento, pode alcançar o endpoint de aprovação.
- Antes de produção, criar contas/papéis de gestão e proteger os endpoints com Policy ou middleware.
- O frontend pode ocultar ações sem permissão, mas essa regra não substitui a proteção do backend.

## Verificações executadas

- ESLint passou nos componentes Vue alterados.
- `quasar build` foi concluído com sucesso.
- A atualização visual foi conferida pela ligação entre o evento do componente e a recarga da página.
- O seeder CODEX fornece um motorista aprovado com os quatro documentos para comparação no painel.
- A regra de aprovação continuou coberta pela suíte completa do backend.
- O build alerta que `src/boot/axios.js` também é importado diretamente; funciona, mas impede separar esse módulo em chunk próprio.

## Observação para o Claude

- Este documento e os comentários marcados com `CODEX` identificam exclusivamente o trabalho do CODEX.
- As regras de autorização e validação rígida continuam pendentes de uma decisão de produto para produção.

## CODEX — Integração com o backend (19/09/2026)

- A edição de usuário pela tela de gestão agora grava nome, e-mail, telefone, CPF e nascimento no endpoint `PUT /users/{id}`. O campo `skill` enviado pela tela não possui coluna correspondente e é ignorado.
- A troca de foto só remove o arquivo anterior após validar e salvar o novo; SVG e arquivos acima de 5 MB são rejeitados. Arquivamento em lote é atômico e rejeita IDs inexistentes.
- Por decisão do usuário, qualquer conta autenticada continua autorizada a gerir usuários/documentos no desenvolvimento. É uma restrição de acesso pendente para produção.
- A restauração de usuários arquivados agora é funcional e atômica. A exclusão permanente responde HTTP 501, para não simular sucesso em uma operação destrutiva ainda sem política definida.
