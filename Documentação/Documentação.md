# Link para o Diagrama;

< PENDENTE INSERIR LINK>


# Análise do código

Analisando o código do repositório do ToggleMaster é visto que ele foi montado como um monolito. A gente percebe isso porque toda a lógica da plataforma, desde a parte de verificação de saúde até o gerenciamento das flags, está concentrada em um único arquivo app.py. Além disso o Dockerfile e o script de entrada mostram que a aplicação roda como um bloco único, sem separar as funções em serviços diferentes (como por exemplo o banco da aplicação) além de que tudo depende de uma conexão só com o banco de dados e é executado pelo mesmo servidor.

Sobre usar essa estrutura para um MVP, a grande vantagem seria a velocidade de build, para quem está começando fazer um monolito isso permite focar na lógica de entrega sem ter que perder muito tempo com a comunicação entre vários serviços ou configurar várias ferramentas ou ambientes. É muito mais simples de rodar e testar, como o próprio README mostra com o Docker Compose. Sem contar que o custo na AWS acaba sendo menor no início, já que você não precisa de tantas instâncias.

Mas em contrapartida o problema seria quando o sistema começar a crescer, pois se um pedaço da aplicação der problema ou tiver um bug pesado, o sistema inteiro pode cair e deixar tudo indisponível. Outro ponto é que a gente fica preso às tecnologias escolhidas no começo, se no futuro precisar de algo muito específico que o Python não resolva pode ser necessário um planejamento mais demorado para adaptar ou mudar a tecnologia.

# Metodologia dos 12-Factor App

 Fatores que a aplicação já atende atualmente:

- Base de Código: O projeto usa um repositório único para gerar o deploy tanto no Docker local quanto na EC2 da AWS.
- Configurações: O app usa variáveis de ambiente como o DB_HOST e DB_PASSWORD para se conectar ao banco.
- Exportação de Serviços: A aplicação é independente e expõe o serviço diretamente pela porta 5000.
- Paridade entre Desenvolvimento e Produção: O Docker garante que o ambiente que o desenvolvedor usa seja igual ao que vai rodar na nuvem.
- Logs: O app joga as informações no console, permitindo a coleta por ferramentas de monitoramento externas.

 Fatores que precisa ajustar na aplicação;

- Dependências: O arquivo requirements.txt lista os pacotes, mas não trava as versões exatas de todas as sub dependências, por exemplo se Flask tiver uma dependência interna que não está travada, o instalador vai baixar a versão mais recente. Se alguém tentar instalar o mesmo projeto daqui a um mês, pode ser que uma dessas sub dependências tenha atualizado e quebre o pacote.
- Serviços de Apoio: o código do aplicativo não tem nenhuma reconexão automática, caso o banco cair com o app rodando por exemplo.
- Build, Release, Run: Atualmente o deploy é manual, teria que separar a criação da imagem das demais fases.
- Processos: O código depende de arquivos e variáveis temporárias, teria que deixar totalmente independente no futuro.
- Concorrência: A aplicação não tem nenhum balanceamento de carga.
- Descartabilidade: O código não trata sinais de desligamento,em vez de terminar as tarefas pendentes.
- Processos Administrativos: Tarefas como criar as tabelas do banco, deveriam ser automáticas no processo de deploy.
