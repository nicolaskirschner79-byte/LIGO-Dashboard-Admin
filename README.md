# LIGO Control — Painel Administrativo

Dashboard exclusiva para administrar o jogo **LIGO — O Desafio dos Lasers**, da feira de ciências.

O painel foi separado do repositório do jogo, mantendo a conexão com o mesmo projeto Supabase.

## Estrutura

- `index.html` — dashboard completa em **um único arquivo HTML**, incluindo CSS e JavaScript.
- `supabase/functions/ligo-admin/index.ts` — código-fonte da função administrativa implantada no Supabase.

## Funcionalidades

- Indicadores: participantes cadastrados, partidas em andamento e concluídas, melhor tempo.
- Rankings separados por dificuldade: Fácil, Médio e Difícil.
- Busca, filtro e exportação de rankings em CSV.
- Histórico de partidas recentes e operações administrativas.
- Exclusão individual dos resultados de um participante em uma dificuldade.
- Limpeza de um ranking específico ou de todos os rankings da versão atual.
- Confirmações obrigatórias antes de excluir.
- Atualização automática por Supabase Realtime e sincronização de segurança a cada **12 segundos**, sem recarregar a página.

## Instalação e publicação

A interface é autossuficiente: abra ou hospede `index.html`. Não precisa de dependências ou compilação. Requer internet e conexão com o projeto Supabase existente.

Para usar GitHub Pages, habilite o Pages nas configurações deste repositório e publique a pasta raiz da branch principal. Configurar a hospedagem **não é necessário** para que o arquivo exista no GitHub.

### Administrador único

Somente o e-mail autorizado na tabela privada `public.ligo_admin_allowlist` pode acessar a dashboard. O banco agora possui uma restrição que não permite adicionar outro e-mail nessa lista. A Edge Function verifica em cada solicitação se o token de acesso é válido, se o e-mail está confirmado e se pertence à única conta autorizada.

**A opção pública "Criar conta" foi removida do HTML.** A dashboard exibe apenas o login e a recuperação de senha. A senha não fica armazenada no código: o Supabase Auth guarda a credencial de forma protegida.

**Para provisionar o único administrador:** acesse o projeto LIGO no [Supabase Authentication → Users](https://supabase.com/dashboard/project/jgjgmbsgkwdimiinhcbk/auth/users), utilize a opção de adicionar/criar usuário por e-mail, informe o e-mail já autorizado na allowlist e defina a senha diretamente na área de administração. Confirme a conta conforme o fluxo exibido. Depois, entre na dashboard normalmente.

A criação programática desse usuário não estava disponível nas ações da integração e a tentativa de escrita direta no schema Auth foi bloqueada por segurança. Portanto, **não considere a senha configurada nem o login testado até criar o usuário pelo painel Supabase**. Como a senha foi enviada numa conversa, recomenda-se trocar por uma senha forte e exclusiva.

O uso de uma única conta **não garante exclusividade física**: qualquer pessoa que tenha acesso às credenciais ou à sessão autenticada poderá entrar. Proteja a conta de e-mail e encerre a sessão quando terminar.



Configure em **Supabase → Authentication → URL Configuration** a URL da hospedagem caso os links de confirmação/recuperação de senha redirecionem a outro domínio.

## Segurança

- O HTML contém apenas a chave **publishable**, adequada ao navegador. **Nunca** insira a chave `service_role` ou credenciais de administrador no HTML.
- Todas as consultas administrativas e exclusões passam pela Edge Function `ligo-admin`.
- O backend valida o token do usuário no Supabase Auth, a confirmação do e-mail e a lista privada de administradores.
- As funções de exclusão do banco não são acessíveis diretamente pelos papéis públicos `anon` ou `authenticated`.
- Exclusões são registradas no log de auditoria.
- Limpar um ranking remove partidas **concluídas** da versão `challenge-v2`; cadastros, partidas em andamento e dados de versões antigas permanecem.
- A ação de limpeza é permanente. Exporte os rankings em CSV antes de executar uma operação destrutiva.
- Recomenda-se manter o repositório **privado** para evitar exposição desnecessária do código administrativo. A autorização da API deve permanecer obrigatória mesmo em repositórios privados.

## Projetos

- [Jogo LIGO](https://github.com/nicolaskirschner79-byte/Jogo-feira-de-ciencias)
- [Projeto Supabase LIGO](https://supabase.com/dashboard/project/jgjgmbsgkwdimiinhcbk)

### Estado de validação

Os arquivos foram transferidos com comparação exata de conteúdo (SHA de blob), preservando a versão anterior. A função administrativa já estava publicada no Supabase. A tela de cadastro foi retirada. O banco mantém a allowlist de um único e-mail, mas ainda falta provisionar o usuário Auth pelo Supabase e testar o login real e as operações administrativas no navegador.
