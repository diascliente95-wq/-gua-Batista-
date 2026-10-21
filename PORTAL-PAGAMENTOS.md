# Portal do cliente — CRM Abastecimento de Água

## Arquitetura planejada
- `portal/index.html`: interface responsiva do morador.
- API privada: solicita e valida código de acesso, retorna somente as faturas do titular e cria cobranças.
- Banco de dados central: cadastro, faturas, pagamentos e registros de auditoria.
- Provedor de cobrança: Pix dinâmico e boleto; webhook assinado para confirmação.

## Estado desta versão
A interface foi adicionada, mas **não está pronta para receber pagamentos reais**. As rotas `/api/portal/solicitar-codigo`, `/api/portal/validar-codigo` e `/api/portal/faturas` precisam ser implementadas e implantadas em um servidor. Nenhum QR Code Pix ou boleto falso deve ser exibido como cobrança válida.

## Provedor recomendado para começar
Avalie o Asaas para Pix e boleto. Abra uma conta empresarial/comercial apropriada e conclua a verificação da conta. Confirme tarifas, regras de uso e requisitos de cadastro diretamente com o provedor. Depois, configure a API key somente como variável secreta do servidor, nunca no HTML nem no GitHub.

## Requisitos obrigatórios antes de produção
1. Banco central (por exemplo, Firestore ou PostgreSQL) e migração controlada dos dados atuais. Os dados salvos em `localStorage` no navegador não ficam automaticamente disponíveis ao portal.
2. Verificação do CPF + código de uso único enviado para telefone/e-mail previamente validado; respostas genéricas para evitar enumeração de CPFs; expiração, limite de tentativas e rate limiting.
3. Sessões seguras com cookies HttpOnly, Secure e SameSite; autorização no servidor para cada fatura. Não confiar em CPF, matrícula ou ID enviados pelo navegador.
4. Criar cobrança Pix/boleto no servidor, associada à fatura e a um identificador único. Exibir QR Code/código copia-e-cola e PDF/link retornados pelo provedor.
5. Webhook validado conforme documentação oficial do provedor, idempotente e registrado em logs. Só marcar como pago após evento confirmado no servidor; nunca confiar em redirecionamento do navegador ou comprovante enviado pelo usuário.
6. Testar primeiro em sandbox, inclusive pagamentos repetidos, cancelamentos, cobranças vencidas e falha de webhook.
7. Publicar sob HTTPS. GitHub Pages sozinho não executa APIs PHP/Node nem guarda segredos.

## Próxima etapa de implantação pelo celular
1. Abra o repositório e confira a branch `feature/portal-cliente-pagamentos`.
2. Faça revisão da alteração e só depois integre à branch principal.
3. Escolha onde executar a API (por exemplo, Vercel Functions) e um banco de dados. O site estático sozinho não realiza autenticação segura nem pagamentos.
4. Cadastre as variáveis secretas no painel do servidor, nunca em arquivos versionados.
5. Configure a URL de webhook HTTPS no painel do provedor e teste em sandbox antes de liberar aos moradores.

## Segurança e privacidade
CPF é dado pessoal. Minimize a coleta, restrinja acesso administrativo, estabeleça política de retenção e disponibilize aviso de privacidade compatível com a LGPD.
