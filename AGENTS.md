# Frontend Gestão — Diretrizes para Agentes

## Stack
- Vue 3.5, Quasar Framework 2.16 (Vite), Pinia, Axios

## Execução e Conectividade
- Iniciar servidor de desenvolvimento:
  ```bash
  npm run dev
  ```
- *Atenção à Porta*: `quasar.config.js` configura a porta `8080`. Caso o serviço Laravel Reverb (WebSocket) esteja rodando na mesma porta no host, execute com porta alternativa ou deixe o Vite auto-incrementar.

## Validação
- Linter: `npm run lint`
- Build: `npm run build`
