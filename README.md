# Lapidar Barbearia

Aplicativo web da Lapidar Barbearia, em português do Brasil e pensado primeiro para celulares. Reúne a página inicial, a galeria de cortes masculinos e infantis, a lista de serviços com preços, as informações de horário e pagamento e o agendamento online com horários reais salvos em banco de dados.

## O que tem no site

- **Página inicial** (`/`): capa, serviços e preços, galeria (abas Masculino e Infantil), horários de atendimento, formas de pagamento e formulário de agendamento.
- **Agendamento**: o cliente informa nome, WhatsApp, serviço, data e horário. Os horários seguem o funcionamento da barbearia (segunda a sábado, 8h às 20h, de 30 em 30 minutos; último horário às 19h30). Horários já reservados aparecem riscados. A confirmação só aparece **depois** que o agendamento é salvo no banco; se algo falhar, o cliente é avisado de que o horário não foi reservado.
- **Agenda da equipe** (`/agenda`): lista compartilhada dos próximos agendamentos, protegida por senha, com atalho para o WhatsApp do cliente e opção de cancelar (o horário volta a ficar livre).

Pagamentos são sempre presenciais: Pix, dinheiro ou cartão.

## Como configurar a agenda compartilhada

Todos os agendamentos ficam em um único banco de dados (Netlify Database), então qualquer pessoa da equipe que abrir a agenda vê as mesmas informações.

1. No painel do Netlify, abra o projeto e vá em **Project configuration → Environment variables**.
2. Clique em **Add a variable**, use o nome `AGENDA_SENHA` e defina uma senha forte para a equipe. Marque como segredo, se a opção aparecer.
3. Faça uma nova publicação (**Deploys → Trigger deploy**) para a senha passar a valer.
4. Envie para os barbeiros o endereço `https://SEU-SITE.netlify.app/agenda` e a senha. No celular, dá para usar “Adicionar à tela inicial” para abrir a agenda como um app.
5. Toque em **Atualizar** para ver novos agendamentos. Para trocar a senha, altere a variável e publique novamente.

Enquanto `AGENDA_SENHA` não estiver configurada, a agenda fica bloqueada para todos.

Dois clientes não conseguem reservar o mesmo dia e horário: o banco recusa o segundo pedido e o site pede para escolher outro horário.

## Tecnologias

- TanStack Start (React 19) com Vite e Tailwind CSS 4
- Netlify Database (Postgres gerenciado) com Drizzle ORM
- Netlify Image CDN para servir as fotos otimizadas
- Componentes shadcn/ui e ícones lucide-react

## Rodando localmente

```bash
pnpm install
netlify dev
```

O `netlify dev` emula o banco de dados e o Image CDN localmente. Para testar a agenda, crie um arquivo `.env` com `AGENDA_SENHA=sua-senha`.

## Publicação no Netlify

O projeto já está pronto: o `netlify.toml` define o build (`vite build`) e a pasta publicada. As migrações do banco em `netlify/database/migrations/` são aplicadas automaticamente a cada publicação.

## Próximos passos possíveis

- Lembrete automático pelo WhatsApp antes do horário.
- Escolha do barbeiro no agendamento, com agenda separada por profissional.
- Bloqueio de dias de folga e feriados pela própria agenda.
