# ASLICINE v23 — Candidato de produção

Esta versão integra o módulo de séries/temporadas/episódios ao aplicativo do usuário, mantendo a v8 e o teste grátis de 7 dias.

## App
- Login e criação de conta (sem acesso de convidado).
- 7 dias grátis sem cartão e sem cobrança automática.
- Home com filmes e séries publicados.
- Aba Séries com catálogo.
- Detalhe da série com temporadas e episódios.
- Player HLS/URL de vídeo via `video_player`.
- Perfil e logout.
- Usuário administrador pode abrir o painel administrativo.

## API
Mantidas as rotas de filmes, séries, detalhe de série, temporadas/episódios e assinatura.

## Executar
```bash
docker compose up -d postgres
cd api
npm install
cp .env.example .env
npm run dev
```

No Flutter:
```bash
cd app
flutter pub get
flutter run
```

Para celular físico, altere `app/lib/config.dart` para o IP do computador na rede local.

### Direitos de conteúdo
Use somente filmes, séries, episódios, trailers, imagens, áudios e legendas que você tenha direito de distribuir/licenciar ou que sejam de domínio público. O aplicativo não concede direitos de distribuição.


## v11 — Player completo e transmissão
- Player com play/pause, avanço/retrocesso de 10s, barra de progresso e salvamento de histórico.
- Menus para qualidade, áudio, legendas e velocidade.
- Bloqueio dos controles.
- Interface de transmissão preparada para Google Cast/Chromecast, Google/Android TV, AirPlay, Fire TV e espelhamento.
- A camada `app/lib/cast_service.dart` isola a integração nativa; a descoberta real de dispositivos deve ser ligada ao SDK/protocolo escolhido antes da publicação.

A documentação atual do ecossistema Flutter confirma que Google Cast/AirPlay exigem configuração nativa e permissões de rede local; não considerar o botão de transmissão desta versão como integração de produção até essa etapa ser concluída.

## v12 — Receptor Google Cast personalizado

Foi adicionado `cast_receiver/` com o receptor Web do ASLICINE. Ele fornece a identidade visual do ASLICINE e usa o Cast Application Framework para reprodução MP4/HLS. Para produção, hospede esse receptor em HTTPS e registre um Application ID no Google Cast Developer Console.

O App ID do aplicativo Flutter pode ser informado por:
`flutter run --dart-define=ASLICINE_CAST_APP_ID=SEU_APP_ID`

O receptor personalizado ainda precisa ser hospedado/registrado e testado em um Chromecast/Google TV real antes de ser considerado pronto para produção.


## v14 — Reprodução protegida
- Endpoint autenticado `POST /api/playback/ticket`.
- Verificação de usuário + trial/assinatura antes da reprodução.
- Token de reprodução de curta duração (10 minutos por padrão).
- App usa o URL autorizado tanto no player local quanto no Google Cast.
- Receptor customizado rejeita LOAD sem `aslicine_token`.

**Produção:** o servidor/CDN de mídia também deve validar o token. A API não consegue tornar um URL externo privado apenas adicionando um parâmetro; a proteção efetiva precisa estar no origin/CDN que entrega os bytes.


## v15 — assinatura mensal Mercado Pago
- checkout mensal preparado via API de Assinaturas do Mercado Pago
- webhook de sincronização de status
- botão de assinatura no fluxo de 7 dias grátis
- credenciais ficam apenas no backend
- preço/plan ID permanecem configuráveis


### Assinatura
Plano mensal definido: **R$ 14,99/mês**, com 7 dias grátis sem cartão e sem cobrança automática.


## Mercado Pago configurado

Plano informado: **ASLICINE Premium — R$ 14,99/mês**.
Link público configurado como `MP_PUBLIC_PLAN_URL=https://mpago.la/2mGUkHR`.
O botão de assinatura abre esse checkout. Para produção, a ativação automática por usuário ainda deve usar o `MP_PREAPPROVAL_PLAN_ID` e webhook do Mercado Pago.


## ASLICINE v19 — Finalização de segurança e publicação
- Direitos de distribuição agora existem também para séries e episódios.
- Publicação de série exige direitos confirmados.
- Publicação de episódio exige direitos confirmados + vídeo.
- Catálogo público e ticket de reprodução ignoram conteúdo sem autorização/publicação.
- Ativação manual de assinatura foi bloqueada; somente checkout/webhook do gateway deve ativar acesso.
- Endpoint `/api/subscription/providers` informa gateways configurados.
- Mercado Pago continua desacoplado; PagBank pode ser adicionado por checkout sem reescrever o app.

### Para produção ainda é necessário
1. Hospedar API/PostgreSQL.
2. HTTPS e CDN/origem que valide `aslicine_token`.
3. Configurar domínio e URLs de webhook.
4. Configurar Google Cast Receiver/App ID.
5. Configurar gateway de pagamento e webhook.
6. Rodar build Android/iOS com Flutter SDK.
7. Publicar somente conteúdo com direitos de distribuição.


## v20 — revisão final da base
- Schema PostgreSQL corrigido.
- Regras de direitos aplicadas também ao detalhe de filmes.
- Atualização de séries corrigida.
- CORS e segurança HTTP configuráveis.
- Proteção básica contra excesso de requisições.
- Validação de segredos em produção.
- Ticket de reprodução limitado a no máximo 15 minutos.


## v22 — preparação final
- Ticket de reprodução de filmes agora exige `rights_confirmed=true`, assim como episódios.
- Checkout Mercado Pago prioriza o fluxo de API quando `MP_ACCESS_TOKEN` + `MP_PREAPPROVAL_PLAN_ID` + `MP_BACK_URL` estiverem configurados. O link público fica apenas como fallback.
- Webhook de pagamentos ganha proteção contra processamento duplicado do mesmo evento.
- O plano público do Mercado Pago não ativa automaticamente uma conta: a confirmação automática depende do fluxo de API + `external_reference` + webhook.
- Antes da publicação, executar testes em ambiente real de homologação e validar o origin/CDN de vídeo contra `aslicine_token`.


## v23 — candidato de produção
- Dockerfile da API com Node 20 e healthcheck.
- Compose separado para produção, sem expor PostgreSQL publicamente.
- Exemplo de ambiente de produção separado do ambiente local.
- Checklist de implantação HTTPS, CDN de vídeo, Mercado Pago, Cast/AirPlay e testes reais.
- Script de verificação de segredos e sintaxe da API.
