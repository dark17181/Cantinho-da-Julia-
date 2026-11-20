# O Cantinho da Julia (versão Vercel)

Site com 20 posições para filmes. Os vídeos MP4 e as capas ficam no **Vercel Blob** (armazenamento do mesmo projeto na Vercel). Os dados dos filmes ficam em um `catalog.json` no mesmo Blob. Qualquer visitante com o link vê e assiste, de qualquer lugar.

## Estrutura

- `public/index.html`: o site (design roxo e funções originais, mais painel com 20 posições).
- `api/login.js`, `api/logout.js`: login do administrador por senha, com cookie assinado.
- `api/blob.js`: libera o envio direto do celular para o Blob (só administrador).
- `api/items/index.js`: lista os filmes publicados (visitantes veem só as posições preenchidas).
- `api/items/[id].js`: preenche ou edita a posição `slot-1` a `slot-20`. Não existe remoção.
- `api/chat.js`: assistente de IA (opcional).
- `package.json`, `vercel.json`, `.env.example`.

## Publicar (uma vez)

1. Crie um repositório no GitHub e envie esta pasta inteira (o `.gitignore` já protege segredos).
2. Na Vercel: **Add New > Project**, escolha o repositório e clique em **Deploy**.
3. No projeto: **Storage > Create > Blob** (acesso **Public**) e conecte ao projeto. Isso cria `BLOB_READ_WRITE_TOKEN` sozinho.
4. **Settings > Environment Variables**, adicione:
   - `ADMIN_PASSWORD`: sua senha de administrador (longa).
   - `SESSION_SECRET`: um texto aleatório longo.
   - `ANTHROPIC_API_KEY`: só se quiser o assistente de IA (opcional).
5. **Deployments > ... > Redeploy** para as variáveis valerem.

## Adicionar um filme

1. Abra o link do site, toque em ⚙ e entre com a senha.
2. Em **Conteúdo**, toque em **Preencher** na posição desejada.
3. Escolha o MP4, a capa e preencha título, sinopse, data de lançamento, gênero e classificação indicativa.
4. Toque em **Publicar** e espere a barra de progresso chegar em 100% (mantenha a tela aberta).
5. O filme aparece para todos os visitantes e continua lá mesmo se você fechar o navegador ou trocar de celular.

Para publicar uma nova versão do código, basta enviar a alteração ao GitHub: a Vercel publica sozinha. Os filmes não são afetados, pois ficam no Blob.

## Limites e custos (conferidos na documentação da Vercel em 16/11/2026)

- Arquivo máximo no Blob: 5 TB. O envio vai direto do celular, sem passar por função, então 1 GB funciona.
- **Plano Hobby (grátis): 1 GB de armazenamento e 10 GB de transferência por mês.** Isso não comporta 2 filmes de 700 MB a 1 GB, e ao estourar o limite o Blob fica bloqueado por 30 dias. Para os filmes, use o plano Pro.
- Pro: armazenamento a US$ 0,023 por GB/mês e transferência a US$ 0,05 por GB (região iad1). Arquivos acima de 512 MB não ficam em cache, então cada exibição também gera cobrança de origem (US$ 0,06 por GB). Estimativa grosseira: cerca de US$ 0,10 a 0,11 por exibição completa de 1 GB.
- Use o Spend Management da Vercel para limitar gastos.
- Formatos aceitos para vídeo: MP4 (H.264 + AAC é o mais compatível) e WebM.

## Retirar um filme

O painel não remove filmes, como combinado. Para retirar um, apague o arquivo no painel da Vercel (Storage > Blob) e peça a alteração do `catalog.json`.

## Segurança

- A senha só existe na Vercel (variável de ambiente), nunca no código.
- O cookie de sessão é assinado, `HttpOnly`, `Secure` e `SameSite=Strict`.
- Só o administrador logado consegue enviar arquivos ou alterar posições.
- O endereço dos vídeos é público (quem tiver o link direto do arquivo consegue abri-lo), o que é inerente ao Blob público.
- Favoritos, configurações e histórico do chat continuam salvos só no navegador de cada pessoa.
