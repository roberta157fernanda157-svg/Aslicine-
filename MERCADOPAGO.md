# ASLICINE — Mercado Pago (assinatura mensal)

A integração desta versão usa a API de Assinaturas do Mercado Pago. A API suporta assinaturas recorrentes e o checkout retorna um `init_point` para o usuário concluir a assinatura.

## Configuração
1. Crie uma conta de vendedor e uma aplicação no Mercado Pago.
2. Crie um plano de assinatura mensal e copie o `preapproval_plan_id`. O Mercado Pago informa também um `init_point` para o plano.
3. No servidor, configure no `.env`:
   - `MP_ACCESS_TOKEN`
   - `MP_PREAPPROVAL_PLAN_ID`
   - `MP_BACK_URL`
   - `MP_MONTHLY_PLAN_NAME` (opcional)
4. Publique o endpoint `POST /api/webhooks/mercadopago` em HTTPS e configure as notificações de assinatura/pagamento conforme a documentação do Mercado Pago.

## Fluxo do ASLICINE
- O usuário recebe os 7 dias grátis sem cartão.
- Depois do teste, ele escolhe `Assinar mensalmente`.
- O backend cria uma assinatura pendente vinculada ao usuário por `external_reference`.
- O app abre o checkout do Mercado Pago.
- O webhook consulta a assinatura no Mercado Pago e atualiza o acesso local para `active`, `pending` ou `cancelled`.
- O ASLICINE não deve liberar conteúdo pago apenas porque o usuário voltou da tela de checkout; o status deve ser confirmado pelo backend/webhook.

## Segurança
Nunca coloque `MP_ACCESS_TOKEN` dentro do aplicativo Flutter. Ele deve permanecer somente no backend.

A documentação oficial do Mercado Pago descreve `POST /preapproval`, `GET /preapproval/{id}` e os eventos de assinatura/pagamento.


## Valor definido pelo ASLICINE
O plano mensal do aplicativo foi definido em **R$ 14,99/mês**. Configure esse mesmo valor no plano criado no Mercado Pago. O backend usa o `MP_PREAPPROVAL_PLAN_ID` para iniciar o checkout; o valor efetivo deve ser conferido no plano do Mercado Pago antes de colocar em produção.


## Link do plano criado

O plano informado pelo proprietário do ASLICINE está em **R$ 14,99/mês** e usa este link público:

`https://mpago.la/2mGUkHR`

Para o primeiro teste, coloque esse endereço em `MP_PUBLIC_PLAN_URL`. O botão **Assinar mensalmente** do aplicativo abrirá o checkout do Mercado Pago.

### Importante sobre ativação automática

O link público é adequado para testar/receber o pagamento, mas **não identifica automaticamente qual usuário do ASLICINE realizou a assinatura**. Para produção, a integração deve migrar para o fluxo de API com `MP_PREAPPROVAL_PLAN_ID`, `external_reference` e webhook, para que o servidor consiga vincular e ativar a assinatura da conta correta.
