# Coleta Operacional — GitHub Pages + Supabase (Realtime)

Este projeto entrega um site **100% estático** (GitHub Pages) conectado ao **Supabase** para:
- Autenticação via link mágico
- Inclusão/Edição de dados por múltiplos usuários **ao mesmo tempo**
- **Realtime** (atualizações instantâneas) e **Presence** (quem está online na mesma unidade)
- **Controle de concorrência otimista** via coluna `version` para evitar sobrescritas invisíveis

## 1) Criar projeto no Supabase
1. Crie um projeto em https://supabase.com
2. No painel, abra **SQL Editor** e rode o conteúdo de `schema.sql` deste repositório.
3. Em **Authentication → Providers**, habilite **Email** (Magic Link). Opcional: OAuth (Google/Microsoft).
4. Em **Authentication → URL Configuration**, defina:
   - **Site URL** = a URL do seu GitHub Pages, ex.: `https://seuusuario.github.io/seu-repo/`
   - **Redirect URLs**: inclua a mesma URL acima.
5. Em **Project Settings → API**, copie:
   - `Project URL`
   - `anon public key`

> ⚠️ As _políticas RLS_ do `schema.sql` estão em modo **MVP** (qualquer usuário autenticado pode inserir/editar).
> Depois endureça isso usando `unit_members` ou restrição por domínio (`@suaempresa.com.br`).

> ✅ O arquivo `schema.sql` já inclui as políticas para **inserir/atualizar o próprio profile** e o comando
> `alter publication supabase_realtime add table public.kpi_entries;` para habilitar o Realtime da tabela.

## 2) Publicar no GitHub Pages
1. Crie um repositório no GitHub (público ou privado).
2. Faça upload dos arquivos:
   - `index.html`
   - `schema.sql`
   - `README.md`
3. Edite `index.html` e substitua:
   ```js
   const SUPABASE_URL = "YOUR_SUPABASE_URL"
   const SUPABASE_ANON_KEY = "YOUR_SUPABASE_ANON_KEY"
   ```
4. Em **Settings → Pages**, ative o Pages na **branch `main`** (pasta `/root`).

## 3) Testar
1. Abra sua URL do GitHub Pages
2. Informe seu e-mail corporativo no formulário. Você receberá um **link mágico** do Supabase.
3. Depois de logado:
   - A lista de **Unidades** virá da tabela `units`.
   - Ao escolher uma unidade, você verá quem está **online** (Presence) e os registros existentes.
   - Inclua um registro e edite outro para testar **Realtime** + **concorrência** (campo `version`).

## Dúvidas comuns
- **Não aparecem updates em tempo real**: confirme no Studio → **Database → Replication** ou **Database → Realtime** que a tabela `public.kpi_entries` está na publicação `supabase_realtime`.
- **Erro ao criar perfil**: verifique se as políticas para `profiles` (insert/update) estão ativas (já incluímos no `schema.sql`).
- **Link mágico não redireciona**: ajuste `Site URL` e `Redirect URLs` para a URL do GitHub Pages.