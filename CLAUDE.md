# Manequim Digital

App (PWA, instalável no celular e no computador) para acompanhar o trabalho de uma costureira e estilista de uma loja de noivas. Ela faz vestidos sob encomenda e para coleção, ajustes nos vestidos de noiva do acervo de aluguel e vestidos de festa.

O Luis (dono deste repositório) está construindo o app para a esposa. Ainda não é um sistema da empresa: por enquanto **só ela edita**, e as outras pessoas (principalmente a dona da loja) **só visualizam**.

> Antes de qualquer trabalho, leia [docs/contexto-conversa.md](docs/contexto-conversa.md). Ele tem o histórico completo das conversas de planejamento: requisitos, decisões, ideias deixadas para depois e próximos passos.

## Decisões já tomadas

- **Idioma:** conversar com o Luis em português do Brasil. Interface do app também em pt-BR.
- **Stack:** front PWA (provavelmente React + Vite; confirmar com o Luis o que usaram no projeto anterior) hospedado no **Cloudflare Pages**; **Firebase Auth + Firestore** para login e dados; fotos no **Cloudflare R2** (via Worker), comprimidas no celular antes do upload (~480 px, JPEG 0,7). O Luis já usou Firebase + Cloudflare grátis em outro projeto com sucesso. Supabase foi considerado e descartado.
- **Desktop:** o próprio PWA instalado. Tauri só se um dia fizer falta.
- **Modelo central:** um *trabalho* (vestido) é dividido em **partes do corpo** (véu, decote, mangas, corpete, cintura, saia, barra, cauda) mais **extras** livres (bordado, forro, armação, capa). Cada parte tem uma **checklist livre de etapas** (sugestão padrão: talhar, alinhavar, prova, costurar, acabamento). Partes podem ser ligadas e desligadas por vestido. Ajuste, confecção e festa usam o mesmo modelo.
- **Bloqueio** é algo importante no sistema: motivo (falta material, aguardando cliente, aguardando decisão, outro), detalhe e data de início. Os bloqueios por falta de material geram a **lista de compras** da dona.
- **Desfile** é só uma *coleção* (um agrupamento com data-alvo), não o centro do app. O app tem que continuar útil depois do desfile.
- **Perfis agora:** ateliê (edita tudo), dona (conta Google, só leitura), convidados (link só de leitura de uma coleção). Vendedoras e fluxo de aprovação ficam para uma fase futura.

## Arquivos

- `atelie-prototipo.html`: protótipo clicável em HTML/JS puro (um arquivo, dados de exemplo, salva no localStorage). Publicado como artifact em https://claude.ai/artifact/Jr6Q9xbD88kQrpBqJoD5gq. É referência de UX e do modelo de dados, não código de produção.
- `docs/contexto-conversa.md`: histórico e raciocínio completos.

## Como trabalhar aqui

- Prioridade de UX: atualizar uma etapa tem que ser **mais fácil que mandar áudio no WhatsApp**. Botões grandes, poucos campos, foto como principal forma de registro.
- Valide as ideias com o uso real da esposa antes de construir muita coisa.
- Ao tomar uma decisão nova importante, atualize este arquivo e o `docs/contexto-conversa.md`.
