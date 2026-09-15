# Handoff — Site do roteiro Serra Negra → Florianópolis

## O que é

Página estática (`index.html`, single-file) com o roteiro de uma viagem de carro de 3 dias, pra compartilhar com o grupo (esposa + irmã). **Mapa em tela cheia** + painel lateral com o roteiro **contínuo** (não abas por dia). Alfinetes roxos com ícone de cama marcam pernoites (Pontal, Guaratuba, Campeche).

**Stack:** HTML/CSS/JS puro, zero build step, zero dependência de backend.
- Mapa: [Leaflet.js 1.9.4](https://leafletjs.com/) via CDN (unpkg), tiles OpenStreetMap, traçado real via [OSRM](https://project-osrm.org/) público (sem API key)
- Fontes: Google Fonts (`Fraunces` pro display, `Source Sans 3` pro corpo)
- Sem framework, sem npm, sem transpilação

**Deploy:** já rodou em GitHub Pages e InfinityFree sem alteração — é só subir o arquivo. GitHub Pages **não roda PHP**, só HTML/CSS/JS estático (relevante se algum dia migrar).

## Contexto da viagem (dados reais, não inventar)

- **Quem:** o usuário, esposa e irmã (a irmã mora em Floripa, é fumante — por isso tem paradas técnicas explícitas no dia 1)
- **Quando:** 27–29 de dezembro
- **Objetivo:** sair de Serra Negra (SP) e chegar em Florianópolis (bairro Campeche) até o dia 29, passando pela Estrada da Graciosa e por um dia inteiro na Ilha do Mel

### Dia 1 — Serra Negra → Pontal do Paraná (~600km)
Saída 6h. Rota: BR-116 (Régis Bittencourt) **sem entrar em Curitiba** — segue até o Portal da Graciosa (saída da BR-116 / PR-410) e desce a serra até o litoral, overnight em Pontal do Paraná. Almoço no Portal / entorno da Graciosa.

Paradas técnicas: Registro (~08:15) e divisa SP/PR (~10:15). **Sem Itapetininga** — decisão explícita de ir direto na Régis até Registro (evitar entrar na cidade só pra voltar). Coordenadas das técnicas ancoradas na BR-116 pra o mapa seguir a rodovia.

Pontos turísticos: Portal da Graciosa, Mirante Recanto Eng. Lacerda, Recanto Mãe Catira (cachoeira), Centro histórico de Morretes (opcional).

**Mapa:** traçado real das estradas via OSRM público (`router.project-osrm.org`), tiles OpenStreetMap. Dia 2: barco em linha tracejada + trilha a pé; Dia 3: Bombinhas como desvio opcional tracejado.

### Dia 2 — Ilha do Mel (dia inteiro)
Barco Pontal do Sul → Encantadas às 8h (travessia ~30min, barcas de 30 em 30min em alta temporada, 8h–18h). Vai pra Encantadas (não Nova Brasília) pela Gruta. **No bate-volta**, Encantadas→Fortaleza a pé (~8,8 km / 3h30 só ida) não fecha com volta às 17h — o roteiro no site recomenda Gruta cedo + táxi náutico até Brasília (~R$ 25, 10–15 min) + Farol das Conchas (~150 degraus) + Fortaleza (~4 km de Brasília) + táxi de volta a Encantadas pro barco das 17h. Depois disso, seguem de carro até Guaratuba/Matinhos pra pernoitar — decisão consciente de não pagar diária cara na Ilha do Mel.

### Dia 3 — Guaratuba → Florianópolis (~300km)
Rota litorânea: Garuva → Joinville → Barra Velha → Penha → Navegantes → **contorna Balneário Camboriú pela BR-101 sem entrar** (decisão explícita do usuário pra evitar o fluxo de gente) → Porto Belo → Bombinhas (**condicional**: só entram se chegarem em Porto Belo até 15h, porque é desvio de península, não passagem) → Tijucas → Biguaçu → Florianópolis/Campeche.

**Fato relevante de infraestrutura:** a Ponte de Guaratuba abriu em maio/2026, eliminando o ferry-boat Guaratuba↔Matinhos que antes era gargalo de horas em alta temporada. Isso é o que torna esse dia 3 viável no tempo estimado.

## Estrutura de dados no código

Tudo em objetos JS no final do `index.html`:

```js
const PLACES = {
  id: { lat, lng, name, note, photo?, type? }
  // type: "technical" | "optional" | "sleep" (pernoite, alfinete roxo com cama) | ausente = ponto normal
}
const STOPS = [{ id, time, day? }, ...]  // roteiro contínuo; day cria um rótulo sticky na lista
const ROUTE_LEGS = [{ mode: "driving"|"boat"|"foot", ids: [...] }, ...]
```

Clique na lista ou no alfinete sincroniza o painel de detalhe (foto + explicação) e centraliza o mapa. Mobile: painel vira drawer inferior (botão Roteiro/Mapa).

- **Vídeos:** lista `VIDEOS` no JS — cada item tem `youtubeId`, título e `related: [stopIds]`. Aparecem **só no detalhe** ao clicar numa parada relacionada (ex.: Ilha do Mel → [tuCbeWhfaEw](https://www.youtube.com/watch?v=tuCbeWhfaEw)).

- **Custos:** botão **Custos** no header com estimativa do grupo (3 pessoas); campo `cost` em várias paradas; preços do barco no painel da Ilha do Mel. Referências set/2026 (ABALINE R$ 49 ida+volta, estacionamento R$ 25–40, gasolina ~R$ 6,20, pedágios Régis/BR-101, pousadas 27–28/dez). Valores variam — confirmar na hora.

- **Guia Ilha do Mel:** ao clicar nas paradas da ilha, além do barco/vídeo, abre abas **Roteiro / Trilhas / Praias / Comer / Dicas**. Distâncias oficiais (placas): Encantadas–Gruta 0,6 km; Encantadas–Brasília 4,8 km; Brasília–Farol 1,6 km; Brasília–Fortaleza ~4 km; Encantadas–Fortaleza ~8,8 km / 3h30. **Importante:** ida+volta Encantadas↔Fortaleza a pé (~17 km) não cabe no bate-volta das 17h — roteiro recomendado usa táxi náutico Encantadas↔Brasília (~R$ 25, ~10–15 min) pra caber Gruta + Farol + Fortaleza. Comércio concentrado em Encantadas (Fim da Trilha etc.). Limite 5.000 visitantes/dia; Gruta depende de maré baixa.

- **Mapa Ilha (coords OSM):** Terminal Pontal (−25.5667, −48.3579); Trapiche Encantadas (−25.5701, −48.3165); Gruta (−25.5741, −48.3119); Nova Brasília (−25.5425, −48.3055); Farol das Conchas (−25.5391, −48.2908); Fortaleza (−25.5108, −48.3112). Traçado: barco Pontal→Encantadas; a pé Encantadas→Gruta; táxi Encantadas→Brasília; a pé Brasília→Farol e Brasília→Fortaleza; barco Encantadas→Pontal.

- **Modo mapa Ilha:** ao clicar numa parada da ilha, o guia (Roteiro/Trilhas/Praias/Comer/Dicas) abre num **painel flutuante no mapa** (não no rodapé do sidebar). Zoom na ilha, nomes permanentes nos pins e labels de tempo nas trilhas (`ILHA_WALK`). No mobile o drawer fecha pra priorizar o mapa. Fechar com × ou “Ver rota”.

- **Trilhas OSM:** `ILHA_TRAILS` com geometria real do OpenStreetMap (path/footway). Encantadas→Gruta ~0,8 km; Encantadas→Brasília ~5 km (costa leste); Brasília→Farol ~1,7 km (Trilha do Farol); Brasília→Fortaleza ~3,8 km. Em modo Ilha: magenta = a pé, azul tracejado = barco/táxi.

- **Extras:** countdown até 27/dez; botão **Tour** no mapa; **Copiar roteiro** (só clipboard, pra colar no WhatsApp); info do barco + vídeo da Ilha do Mel aparecem **só ao clicar** nas paradas da ilha.

## Pontos de atenção pra quem for mexer

- **Fotos são hotlink direto pra `lh3.googleusercontent.com`** (URLs de Google Places Photos, obtidas via places_search). Funcionam agora, mas são URLs com token — podem expirar ou parar de resolver em algum momento. Se isso acontecer, é preciso rebuscar as fotos (Google Places API ou nova busca) e trocar a URL no objeto `PLACES`.
- Coordenadas das paradas técnicas (Registro, divisa SP/PR) são aproximadas na BR-116, não endereço exato de posto — são só waypoints indicativos no mapa.
- Coordenada de Bombinhas também é aproximada (centro da cidade), sem place_id verificado.
- Sem responsividade testada em telas muito pequenas além do básico (`viewport` + layout fluido) — vale revisar em iPhone SE se for parte de um produto maior.
- `scrollWheelZoom: true` no mapa full-screen (a página é o mapa). No mobile o drawer cobre a parte de baixo.

## Possível próximo passo

Se evoluir pra ter estado (cada um marca o que já fez, RSVP de confirmação por trecho, etc.), aí sim justifica migrar pra PHP + SQLite e hospedar em algo que rode PHP (InfinityFree serve; GitHub Pages não). Até lá, manter estático é a escolha certa — menos superfície, zero manutenção de servidor.
