# Painel de Notas Fiscais — Nagumo

Painel de acompanhamento das notas fiscais de saída entre empresas do Grupo Nagumo
que ainda não têm o evento **210200 – Confirmação da Operação**.

- **Site:** publicado pelo GitHub Pages a partir do `index.html` deste repositório.
- **Dados:** ficam no Firebase (Firestore). A planilha **não** é enviada ao GitHub.
- **Leitura aberta:** qualquer pessoa com o link vê o painel atualizado.
- **Login (e-mail e senha):** necessário para enviar a planilha e escrever observações.

## Atualização diária

1. Exporte a base bruta do sistema fiscal (.xlsx), no formato de sempre
   (`Numero`, `DataEmissao`, `CNPJCPFEmitente`, `RazaoSocialEmitente`, `CnpjCpf`, `RazaoSocial`, `ValorTotal`…).
2. Abra o site e clique em **Entrar** (menu lateral).
3. Na aba **Upload de Dados**, arraste a planilha.
4. Confira o **Resumo da atualização** e clique em **Publicar para todos**.

Como a base é mesclada, sem duplicar notas (chave: CNPJ emitente + CNPJ destinatário + número da nota):

| Situação                                     | Resultado                                  |
|----------------------------------------------|--------------------------------------------|
| Nota nova                                    | entra como **pendente**                     |
| Nota que já estava e continua na base bruta  | continua **pendente**                       |
| Nota pendente que **sumiu** da base bruta    | vira **resolvida** (operação confirmada)    |
| Nota resolvida que **voltou** à base bruta   | é **reaberta**                              |

Notas resolvidas não contam como atraso. Elas aparecem na aba **Notas Fiscais**
pelo filtro *Status → Resolvidas*. As observações ficam ligadas à nota e são
compartilhadas com todos em tempo real.

> Envie sempre a base bruta **completa** do dia. Uma planilha parcial faria as notas
> que ficaram de fora serem marcadas como resolvidas. O resumo avisa quando muitas notas
> vão ser resolvidas de uma vez.

## Configuração do Firebase (uma vez)

Projeto: `relatorio-entre-o-grupo`.

1. **Firestore Database › Regras:** cole o conteúdo de [`firestore.rules`](firestore.rules) e clique em **Publicar**.
2. **Authentication › Método de login:** ative **E-mail/senha**.
3. **Authentication › Configurações › Domínios autorizados:** adicione `cpdnagumocd.github.io`.
4. Entre no site com uma conta criada no console e, na aba **Usuários**, clique em **Tornar-me administrador**. Isso só funciona uma vez.
5. **Authentication › Configurações › Ações do usuário:** deixe **Ativar criação (inscrição)** ligado. O site precisa disso para criar contas pela aba Usuários.
   Não é uma brecha: as regras só deixam editar quem está na lista de usuários do painel.

## Usuários

Na aba **Usuários** (visível só para administradores):

- **Adicionar usuário:** cria a conta e libera a edição. Você pode definir uma senha inicial ou enviar um e-mail para a pessoa criar a própria senha.
- **Enviar link de senha:** para quem esqueceu a senha.
- **Tornar admin / Tirar admin:** administradores também cadastram e removem usuários.
- **Remover acesso:** tira a permissão de edição na hora. Para apagar a conta de vez: console › Authentication › Usuários.

## Limpar todos os dados

No fim da aba **Upload de Dados**, só administradores veem **Limpar todos os dados**.
Essa opção apaga, para todos, a base de notas (pendentes e resolvidas) e todas as observações.
Os usuários e as permissões continuam. Para confirmar, é preciso digitar `LIMPAR`. Não há como desfazer.

## Publicação no GitHub Pages

Em **Settings › Pages** do repositório: *Source: Deploy from a branch*, branch `main`, pasta `/ (root)`.
Cada `git push` na `main` atualiza o site em cerca de 1 minuto.

## Sem internet

Se o Firebase não responder, o painel entra em **modo local**. A planilha é lida
normalmente, mas os dados e as observações ficam só naquele navegador.
