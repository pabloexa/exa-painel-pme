# Estrutura completa — tudo que precisa existir antes do 1º real gasto

## 0. Decisão de arquitetura: pra onde vai o clique?

Dois destinos possíveis, e a resposta é **os dois, com pesos diferentes**:

| | A — WhatsApp direto (CTWA) | B — Formulário (exa-diagnostico) |
|---|---|---|
| Fricção | Mínima (1 clique → conversa) | 7 etapas antes do contato |
| Qualificação | Na conversa (foto da conta) | Automática (respostas do form) |
| Custo/lead | Até 92% menor que LP (benchmark) | Mais caro, porém lead mais "cozido" |
| Otimização Meta | Nativa (objetivo Mensagens) | Exige Pixel + evento Lead |
| Risco | Curioso entra na conversa | Abandono no meio do form |

**Recomendação: 70% da verba em CTWA direto / 30% no formulário**, medindo pelo mesmo indicador (custo por **conta recebida**, não por clique). O formulário também entra em dois outros papéis: (1) link enviado *dentro* do WhatsApp pra quem prefere autoatendimento; (2) destino de retargeting pra quem conversou e sumiu. Depois de 2 semanas de dado, o vencedor leva a verba.

**⚠️ Achados na infra atual (resolver antes de tudo):**
1. **Dois números de WhatsApp diferentes**: o diagnóstico aponta pra (34) 99990-2008 e a calculadora pra (34) 98442-2471. Unificar em UM número oficial de vendas — todo CTA, form e anúncio apontando pro mesmo.
2. **Nenhuma das páginas tem Meta Pixel** — sem ele a campanha B não otimiza e o retargeting não existe.
3. O diagnóstico tem **7 etapas** — ok pra tráfego morno, longo pra frio. Considerar versão curta (3 perguntas: empresa ou casa? faixa de conta? WhatsApp) como variante de teste.

---

## 1. Contas e ativos Meta (Business Manager)

- [ ] **Business Manager** criado (business.facebook.com) com admin de backup (2 pessoas)
- [ ] **Página do Facebook** + **Instagram profissional** da Exa conectados ao BM
- [ ] **Conta de anúncios** em BRL com forma de pagamento + limite de gasto definido
- [ ] **Verificação do domínio** (exaenergia.com e/ou pabloexa.github.io não dá — usar domínio próprio pro form se possível, ex. diagnostico.exaenergia.com via CNAME do GitHub Pages)
- [ ] **Conjunto de dados / Pixel** criado no Gerenciador de Eventos
- [ ] **Verificação da empresa** (Business verification) — CNPJ da Exa; necessária pra API do WhatsApp e aumenta limites
- [ ] **WhatsApp conectado ao BM e à Página** (o número oficial escolhido)

## 2. WhatsApp — o motor da campanha

**Decisão de plataforma:**
- **Fase 1 (semana 1-2):** WhatsApp Business **App** basta — saudação, respostas rápidas, etiquetas, ice breakers funcionam.
- **Fase 2 (semana 3+):** migrar pra **Cloud API** (direto ou via BSP tipo 360dialog/Gupshup) — destrava CAPI de Business Messaging (otimizar por "conta recebida"), automação de qualificação e múltiplos atendentes. A janela de 72h grátis do CTWA vale na API.

**Configuração (App, fazer antes do D0):**
- [ ] Perfil completo: logo oficial, descrição ("Reduzimos a conta de energia de empresas do Triângulo — Lei 14.300/ANEEL"), site, horário
- [ ] **Saudação automática** (a do painel, aba WhatsApp)
- [ ] **Mensagem de ausência** com expectativa honesta ("respondo até às 9h — pode já mandar a foto da conta")
- [ ] **Respostas rápidas**: `/simulacao` (confirmação de conta recebida) · `/planos` (Direto vs Fixo) · `/golpe` (explicação Lei 14.300) · `/followup` (48h) · `/indicacao` (pedir 2 indicações)
- [ ] **Etiquetas** espelhando o HubSpot: Novo lead · Conta recebida · Simulação enviada · Negociação · Fechado · Fora
- [ ] **Ice breakers** definidos no nível do anúncio (os 3 do painel)
- [ ] **Escala de resposta**: quem responde em cada janela de anúncio ativo (SLA < 5 min); anúncio pausado fora dessas janelas

## 3. Formulário (exa-diagnostico) — upgrades necessários

- [ ] **Unificar o número** de destino com o da campanha
- [ ] **Instalar Meta Pixel**: `PageView` no load, `ViewContent` ao iniciar o form, **`Lead` no submit** (é o evento de otimização da campanha B)
- [ ] **Capturar UTMs** (utm_source/medium/campaign/content) e carregar junto com as respostas até o WhatsApp/HubSpot — é o que liga criativo → lead → contrato
- [ ] **Tela final** com botão wa.me com **mensagem pré-preenchida** contendo o resumo das respostas (ex.: "Vim do diagnóstico: empresa, conta ~R$ 6 mil, sem GD") — o vendedor já recebe o lead qualificado
- [ ] **Enviar respostas pro HubSpot** (Forms API ou webhook) criando contato + deal automático com `consumo_medio`
- [ ] Revisar copy do form contra os guardrails (15% só no Direto, 90 dias, sem "grátis"/"garantido")
- [ ] (Opcional) Versão curta de 3 perguntas pra teste com tráfego frio
- [ ] A **exa-calculadora** vira arma do vendedor (mandar o link na negociação) e página de retargeting — mesmo Pixel nela

## 4. Pixel, CAPI e medição

- [ ] Pixel nas duas páginas (diagnóstico + calculadora)
- [ ] Eventos: `PageView` · `ViewContent` · `Lead` (submit) · `Contact` (clique em qualquer botão wa.me)
- [ ] **Padrão de UTM**: `utm_source=meta&utm_medium=paid&utm_campaign=gc-fase1&utm_content=r1-h2` (r=roteiro, h=hook)
- [ ] **Painel de acompanhamento diário** (planilha ou aba nova no painel): gasto · conversas iniciadas · contas recebidas · custo/conta · deals no HubSpot — conferência diária: nº de contas = nº de deals
- [ ] Fase 2: **CAPI de Business Messaging** — HubSpot dispara "conta recebida"/"proposta enviada"/"fechado" de volta pra Meta; campanha passa a otimizar por isso

## 5. HubSpot

- [ ] Pipeline de Consumidores com estágios já usados no manual de campo (Em atendimento → Proposta enviada → Negociação → Recebimento de Docs → …)
- [ ] **`consumo_medio` (kWh) obrigatório** em todo deal — sem isso não soma na meta
- [ ] Propriedade de origem: `trafego-pago` + campo com o `utm_content` (qual criativo gerou)
- [ ] De "Recebimento de Docs" em diante: `distribuidora` (CEMIG) e `produto__plano` preenchidos
- [ ] Rotina: todo lead do form/WhatsApp vira contato + deal **no mesmo dia** (automático via API na fase 2; manual com disciplina na fase 1)

## 6. Criativos — specs de entrega

- [ ] **9:16 1080×1920** (Reels/Stories) — master; export também **4:5 1080×1350** pro feed
- [ ] Legenda queimada (padrão: Inter bold, caixa alta curta, highlight verde #22C55E)
- [ ] Ícone do WhatsApp visível + chat na tela nos primeiros 1,5s quando couber
- [ ] Naming dos arquivos: `R1-h2-m0.mp4` (roteiro-hook-música) — casa com o utm_content
- [ ] 8 roteiros × 2-3 hooks × com/sem música = ~30-40 arquivos na primeira leva (edição em lote)
- [ ] Thumb/primeiro frame nunca preto: rosto + texto do hook

## 7. Montagem no Gerenciador de Anúncios

- [ ] 2 campanhas: **[GC] CTWA** (objetivo Mensagens → WhatsApp, 70%) e **[GC] Form** (objetivo Conversões → evento Lead do diagnóstico, 30%)
- [ ] Conjuntos A/B/C/D conforme o plano de mídia (amplo qualificado / decisores / lookalike / retargeting)
- [ ] Naming: `[GC] CTWA · A-amplo · R1-h2`
- [ ] Posicionamentos Advantage+ (deixar a Meta distribuir), otimização "conversas iniciadas" (CTWA) e "Lead" (form)
- [ ] **Regras automáticas**: pausar anúncio com custo/conversa > 2× meta após R$ 50 gastos; alerta diário de gasto
- [ ] Públicos salvos: LAL 1-3% clientes fechados (exportar do HubSpot), envolvimento 365d, visitantes do diagnóstico 30d

## 8. Cronograma de implantação (D-7 → D0)

| Dia | Entrega |
|---|---|
| **D-7** | BM completo, verificação iniciada, **número de WhatsApp unificado**, Pixel criado |
| **D-5** | Diária de gravação (plano na aba Roteiros) |
| **D-4** | Form atualizado: Pixel + UTM + redirect wa.me + número certo |
| **D-3** | Edição da 1ª leva (R1, R2, R3, R7 + hooks), naming aplicado |
| **D-2** | WhatsApp configurado (saudação, respostas rápidas, etiquetas), HubSpot pronto, escala de resposta definida |
| **D-1** | Campanhas montadas em rascunho + **revisão de guardrails de marca em cada anúncio** (checklist da aba Resumo) |
| **D0** | Publicar terça ou quarta ~9h. Primeiras 48h: só observar e responder < 5 min — zero mexida na campanha |

## O que eu ainda preciso de você (decisões)

1. **Qual número de WhatsApp é o oficial de vendas?** (99990-2008 ou 98442-2471)
2. Nomes **"Plano Direto"/"Plano Fixo"** — validados pra publicar?
3. Quem além de você responde o WhatsApp (pra escala do SLA de 5 min)?
4. Verba total confirmada (o plano assume R$ 70–100/dia)?
5. Domínio próprio pro diagnóstico (ex. `diagnostico.exaenergia.com`)? Melhora verificação de domínio, Pixel e confiança do lead.
