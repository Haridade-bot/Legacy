# DropCentral Pro — guia de configuração

## O que já vem neste pacote

- Interface responsiva futurista em azul e preto.
- Navegação em português.
- Catálogo local de produtos e pedidos, com cálculo de margem estimada.
- Página de suporte por e-mail e Instagram `@dropcentral`.
- Tela de login/cadastro preparada para Supabase Auth.
- Rotas de início de autorização para Mercado Livre e Shopee, que mostram erro de configuração até que as variáveis sejam adicionadas.

## O que ainda NÃO está ativo

- Login real até configurar o projeto Supabase.
- Banco de dados/sincronização de produtos e pedidos entre dispositivos.
- Callback OAuth, armazenamento/renovação segura de tokens e operações de API completas.
- Envio de pedidos, importação automática de produtos, sincronização de estoque ou conexão confirmada com qualquer marketplace.

Não informe senhas de marketplaces no formulário de login. Nunca publique `service_role`, `client_secret`, `partner_key`, access tokens ou refresh tokens no GitHub ou no HTML.

## 1. Publicar no Netlify com GitHub

1. Faça upload destes arquivos para a raiz do seu repositório GitHub.
2. No Netlify, importe o repositório.
3. Build command: deixe vazio (ou `npm install` se a tela exigir).
4. Publish directory: `.`.
5. Functions directory: `netlify/functions` (já definido em `netlify.toml`).
6. Publique. Após cada alteração, aguarde o deploy terminar.

## 2. Ativar login e senha com Supabase

1. Crie um projeto em https://supabase.com/.
2. No painel do projeto, abra as configurações de API e copie a Project URL e a chave pública/anon (publishable/anon, conforme o painel).
3. No `index.html`, substitua:
   - `COLOQUE_A_URL_DO_SUPABASE`
   - `COLOQUE_A_CHAVE_PUBLICA_ANON`
4. Em Authentication → URL Configuration, cadastre a URL do seu site Netlify como Site URL e Redirect URL.
5. Em Authentication → Providers, confirme que Email está habilitado.
6. Teste Criar conta, confirme o e-mail se solicitado e depois entre.
7. A chave pública pode estar no front-end; nunca use a chave `service_role` no HTML.

Este starter usa Supabase Auth para login, mas os produtos e pedidos continuam no navegador. Para contas reais com dados separados por usuário, crie tabelas no Supabase e políticas RLS antes de mover esses dados para o banco.

## 3. Configurar Mercado Livre

1. Entre em https://developers.mercadolivre.com.br/ e crie uma aplicação na conta correta.
2. Configure o redirect URI para ser EXATAMENTE a URL da função de callback que você implementará, por exemplo `https://SEU-SITE.netlify.app/.netlify/functions/mercadolivre-callback`. Não use esse exemplo antes de criar a função.
3. Em Netlify → Site configuration → Environment variables, adicione:
   - `ML_CLIENT_ID` = App ID fornecido pelo Mercado Livre.
   - `ML_REDIRECT_URI` = callback HTTPS exato cadastrado no portal.
4. Faça deploy novamente.
5. A rota `/.netlify/functions/mercadolivre-connect` inicia o redirecionamento para autorização quando as duas variáveis existem.

**Ainda é necessário implementar a função `mercadolivre-callback`** para validar `state`, trocar o código OAuth por tokens no servidor e armazenar/renovar tokens com segurança. Até isso existir, a autorização completa não está pronta e não haverá conexão funcional com a conta.

Documentação oficial: https://developers.mercadolivre.com.br/pt_br/mensagens-post-venda/autenticacao-e-autorizacao

## 4. Configurar Shopee

1. Entre em https://open.shopee.com/ e registre sua conta de desenvolvedor/aplicativo.
2. Obtenha o Partner ID e Partner Key conforme o painel oficial.
3. Configure o redirect URI exato para uma função de callback HTTPS que será implementada.
4. Em Netlify → Environment variables, adicione:
   - `SHOPEE_PARTNER_ID`
   - `SHOPEE_PARTNER_KEY`
   - `SHOPEE_REDIRECT_URI`
5. Faça deploy novamente.

A rota `/.netlify/functions/shopee-connect` prepara o redirecionamento de autorização quando as variáveis existem. **Ainda é necessário implementar a função `shopee-callback`**, validar `state`, trocar o código pelos tokens e armazená-los/renová-los com segurança. Não considere a loja conectada até concluir e testar todo o fluxo.

Portal oficial: https://open.shopee.com/

## 5. Configurar o e-mail de suporte

No `index.html`, procure `svlegacy7@gmail.com` e substitua pelo endereço de suporte que você realmente controla. Teste o link no celular. O botão usa `mailto:`, então abre o aplicativo de e-mail do visitante; para receber chamados dentro do sistema será necessário um serviço de backend.

## 6. Instagram

O link configurado é `https://www.instagram.com/dropcentral/`. Confirme que o endereço corresponde ao perfil oficial antes de divulgar o site.

## 7. Checklist antes de vender de verdade

- [ ] Login e confirmação de e-mail testados.
- [ ] Recuperação de senha configurada.
- [ ] Dados privados separados por usuário com RLS no banco.
- [ ] Callback OAuth implementado e testado para cada marketplace.
- [ ] `state` aleatório validado no callback.
- [ ] Segredos apenas em variáveis de ambiente no servidor.
- [ ] Tokens criptografados/guardados com acesso restrito e refresh seguro.
- [ ] APIs e permissões aprovadas/testadas nas contas oficiais.
- [ ] Testes de importação, estoque, pedido, falha e repetição antes de automatizar vendas.

Este projeto é um starter de desenvolvimento, não um sistema de produção certificado. A interface não deve afirmar que uma conta está conectada até o backend confirmar o fluxo OAuth completo.
