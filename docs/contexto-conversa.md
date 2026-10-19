# Contexto do projeto: histórico das conversas

Resumo fiel das conversas de planejamento entre o Luis e o Claude (19/10/2026), para continuar o trabalho em outro computador sem perder contexto.

---

## 1. O pedido original (palavras do Luis, resumidas)

- Quer planejar um **app PWA e um app desktop**.
- A esposa é **costureira e estilista numa loja de noivas**. O sistema é para ela acompanhar o trabalho pelo celular, com um perfil **admin** e um de **visualização**.
- Os vestidos têm muitos passos (tirar medidas, talhar, costurar…), e "costurar" tem várias etapas. **Partes diferentes do mesmo vestido podem estar em etapas diferentes ao mesmo tempo** (uma parte sendo costurada, outra sendo talhada).
- Vai ter um **desfile no fim do ano**, e a **dona da loja** e outras pessoas interessadas precisam ver o andamento.
- Quer algo **visual e interativo**. Às vezes coisas ficam **bloqueadas por falta de material**.
- O sistema deve englobar as outras atividades dela: **muitos ajustes nos vestidos de noiva de aluguel**, **vestidos de festa**. A loja confecciona e aluga.
- Existem **atritos com as vendedoras**. A loja é bagunçada em processos e fluxos.

## 2. Primeira proposta do Claude (visão completa, nem tudo é para agora)

**Modelo de dados:** Trabalho → Partes → Etapas, em vez de um kanban linear. O progresso do vestido é a soma das partes.

**Tipos de trabalho com roteiros próprios:**
- Confecção (desfile/coleção): muitas partes, prazo fixo.
- Ajuste de aluguel: ficha da cliente → prova 1 → ajuste → prova final → entrega → desfazer o ajuste na devolução. Tem a data do evento da cliente.
- Vestido de festa: parecido com confecção, mais curto; pode virar peça de aluguel.
- **Acervo** (ideia futura): cada vestido de aluguel com foto e histórico (quantas vezes alugado, ajustes, estado, lavanderia).

**Bloqueio como coisa importante no sistema:** motivo, quem destrava, desde quando (contador de dias parado). Os de material viram **lista de compras automática**.

**Perfis (visão de longo prazo):** ateliê/admin, gestão (dona), vendedora (abre pedido de ajuste, vê status, não mexe em etapas), visualização (link sem login).

**Vendedoras (fase futura), causas prováveis do atrito e soluções:**
- Prometem prazo sem ver a fila → o sistema mostra a "próxima data disponível".
- Pedido verbal → **ficha de ajuste obrigatória** (foto, medidas, data do evento, o que foi combinado).
- Mudanças sem registro → histórico/comentários em cada trabalho.
- Aceite do ateliê antes de o pedido valer; regras combinadas com a dona (prazo mínimo, taxa de urgência).
- Alerta dado ao Luis: **o sistema não resolve política sozinho**. A regra "pedido sem ficha não existe" tem que vir da dona.

**Painel do desfile:** grade de looks com foto, progresso e semáforo, contagem regressiva, previsto × real, bloqueios no topo, galeria de bastidores (que pode virar conteúdo para o Instagram da loja).

**Features futuras citadas:** ficha de medidas da cliente, agenda de provas, estoque simples de materiais, métricas (tempo médio por tipo de ajuste, volume semanal; útil para precificar e para mostrar o volume de trabalho da esposa), avisos por WhatsApp para a cliente.

**Fases sugeridas:** 1) o mínimo para o desfile (2–3 semanas); 2) ajustes de aluguel e vendedoras; 3) acervo, estoque, métricas, WhatsApp.

## 3. Respostas do Luis (o que mudou o rumo)

- **Firebase em vez de Supabase?** Sim. Ele já fez um projeto com Firebase + hospedagem grátis na Cloudflare e deu muito certo.
- **Quantos looks no desfile?** Não sabe ainda, vai perguntar. Mas o desfile é pontual (o próximo é só ano que vem). **Vestidos e ajustes ela faz o tempo todo**, então o sistema tem que servir ao dia a dia.
- **Outras costureiras?** Existem, mas **por enquanto o sistema é só para a esposa**. Não há apoio da empresa para adotar na loja toda.
- **Partes e etapas são parecidas entre vestidos?** **Podem ser muito diferentes.** Ele gostou de uma ideia do Gemini: visualizar cada vestido em **blocos/"partes do corpo"**.
- **Vendedoras e dona usam celular?** Usariam se for intuitivo, mas **por ora os outros só visualizam**, principalmente a dona, para acompanhar o que a esposa faz e os travamentos.
- **Stack:** pediu recomendação, com a preferência acima por Firebase + Cloudflare.

## 4. Decisões resultantes

**Firebase vs Supabase:** Firebase. Motivos: experiência prévia do Luis; Firestore com offline de verdade (bom para ateliê com sinal ruim); tempo real simples; o Supabase gratuito pausa projetos inativos. Ponto de atenção: pelo que o Claude sabia, o Firebase Storage passou a exigir o plano Blaze (pago por uso, com cartão, ainda com cota grátis). **Confirmar no projeto.** Por isso as fotos vão para o **Cloudflare R2** (10 GB grátis, sem custo de download), comprimidas no celular.

**Stack recomendada:**
- Front: o mesmo do projeto anterior do Luis (provavelmente React + Vite; **perguntar**), como PWA, no Cloudflare Pages.
- Dados e login: Firebase Auth + Firestore.
- Fotos: Cloudflare R2 + um Worker pequeno para upload.
- Desktop: o próprio PWA instalado; Tauri só se fizer falta.

**Firestore:** **um documento por trabalho**, com zonas, etapas, extras, bloqueios e histórico dentro dele. Uma leitura carrega o vestido inteiro. Campo denormalizado (ex.: `bloqueado: true`) para a dona listar rapidamente o que está travado.

**Permissões agora:** esposa edita tudo; dona entra com conta Google como leitora; convidados recebem link só de leitura de uma coleção. Vendedoras e aprovação ficam para quando a empresa quiser adotar o app.

**O app gira em torno do dia a dia.** O desfile vira uma **coleção** (agrupamento com data-alvo). Ano que vem cria-se "Desfile 2027".

**Partes do corpo (croqui):**
- Silhueta de vestido com **zonas clicáveis** coloridas pelo estado: véu, decote, mangas, corpete, cintura, saia, barra, cauda.
- Zonas **ligadas/desligadas por vestido** (tomara que caia não tem manga).
- **Extras** livres para o que não é parte do corpo: bordado, forro, armação, botões, capa.
- Tocar numa zona abre uma **checklist livre**. O app sugere etapas, mas ela adiciona, remove e renomeia.
- Fotos e notas por zona.
- Serve também para **ajuste**: o mesmo croqui com só 1–2 zonas ativas e etapas como "subir 3 cm", "apertar 2 cm".

**Telas principais:** Hoje · Trabalhos · Vestido (croqui) · Zona (etapas/bloqueio/foto) · Painel só leitura.

## 5. Protótipo (`atelie-prototipo.html`)

Publicado em https://claude.ai/artifact/Jr6Q9xbD88kQrpBqJoD5gq (privado; compartilhar pelo menu Share). Um único HTML com JS puro, dados de exemplo, data fixa em 19/10/2026, estado salvo no localStorage (botão "Restaurar dados de exemplo").

**O que tem:**
- **Hoje:** saudação, faixa do desfile (contagem regressiva com data de exemplo 12/12/2026, nº de looks, bloqueios, progresso), lista "Travado" (com dias parado), "Prazos dos próximos dias" e "Em andamento".
- **Trabalhos:** filtros (Todos, Ajuste, Confecção, Festa, Desfile 2026, Bloqueados).
- **Vestido:** croqui SVG com zonas clicáveis (verde = pronto, âmbar = em andamento, hachurado vermelho = bloqueado, cinza = não começou, tracejado = não se aplica); progresso como **fita métrica**; lista de partes; botão "Partes" para ligar/desligar zonas e adicionar extras; histórico.
- **Folha da zona:** etapas que avançam com um toque (pendente → fazendo → feito), adicionar/remover etapa, bloquear com motivo e detalhe, desbloquear, anexar foto (comprimida no navegador).
- **Novo trabalho (+):** tipo, nome, cliente/destino, prazo e seleção das partes tocando no croqui.
- **Visão da dona** (alternar no topo): somente leitura, grade de looks com mini-croquis, indicadores (ajustes em aberto, entregas em 7 dias, bloqueios), "Precisa de alguém" e **lista de compras** gerada dos bloqueios "Falta material".

**Modelo de dados usado no protótipo (base para o Firestore):**
```js
trabalho = {
  id, tipo: 'Ajuste' | 'Confecção' | 'Festa',
  nome, cliente, colecao?: 'Desfile 2026', prazo: 'YYYY-MM-DD',
  zonas: { veu|decote|mangas|corpete|cintura|saia|barra|cauda: Parte },
  extras: [ { k, n, ...Parte } ],
  hist: [ { d: 'YYYY-MM-DD', x: 'texto' } ]
}
Parte = { on: bool, etapas: [ { t: 'Talhar', s: 'p'|'f'|'ok' } ],
          bloqueio: null | { motivo, detalhe, desde }, fotos: [dataURL] }
// estado da parte: na (off) > block > done (todas ok) > doing (alguma f/ok) > todo
// progresso: ok = 1, fazendo = 0,5, média sobre todas as etapas das partes ligadas
```

**Identidade visual do protótipo:** Bodoni Moda (títulos), Figtree (texto), IBM Plex Mono (números/datas); acento vinho/rosa; modo claro e escuro; detalhe de ofício: barra de progresso desenhada como fita métrica.

## 6. Pendências e próximos passos

1. **Validar o protótipo com a esposa.** Perguntas:
   - As zonas do croqui fazem sentido? Faltam outras (costas, alças)?
   - Ela atualiza uma etapa sem precisar pensar?
   - Os motivos de bloqueio cobrem a realidade dela?
2. Saber **quantos looks** vão para o desfile e a **data real** do desfile.
3. Confirmar o **framework do projeto Firebase anterior** do Luis, para seguir a mesma linha.
4. Ajustar o protótipo com o retorno dela.
5. Montar o app de verdade: PWA + Firebase Auth/Firestore + Cloudflare Pages + R2. Fase 1 = o que está no protótipo, com persistência real e o link/perfil de leitura da dona.
6. Fases futuras: vendedoras e ficha de ajuste, acervo, agenda de provas, ficha de medidas, estoque, métricas, avisos por WhatsApp.
