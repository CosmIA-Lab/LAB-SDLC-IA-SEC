# Roteiro de áudio — Ponto 1: ciclo de vida de desenvolvimento seguro

Narração para escuta. Segue a sequência do argumento, em voz de dissertação.

---

Ponto um. Ciclo de vida de desenvolvimento seguro e DevSecOps.

O ponto inteiro é uma corrente de seis elos, e cada elo puxa o próximo. São eles: o custo cresce com o tempo; por isso, pushing left; falha não é a mesma coisa que bug; existem conceitos de base que valem sempre; eles se distribuem pelas cinco fases; e tudo isso se automatiza no DevSecOps. Guarde essa ordem, porque é ela que torna o resto inevitável.

Primeiro elo: o custo cresce com o tempo. Todo software erra. A pergunta de engenharia não é se a falha existe, é quando ela é descoberta. E o momento define o custo. Uma falha pega no requisito é uma frase reescrita. A mesma falha descoberta em produção é incidente, é perícia, é notificação de clientes. A curva de custo é antiga: Boehm, mil novecentos e oitenta e um.

Daí nasce o segundo elo: pushing left. Trazer a segurança para o início do ciclo. Repare: pushing left não é um princípio, nem uma técnica. É uma estratégia. Ela não restringe nada; ela decide quando aplicar os princípios, e a resposta é: cedo. O nome vem de Tanya Janca, dois mil e vinte. Dois primos: shift-left é pushing left aplicado ao pipeline, testar a cada commit; shift-right é o complemento, monitorar a produção. Um previne, o outro detecta.

Terceiro elo: falha não é bug. Uma falha, flaw, é um erro de desenho. Nasce no requisito ou no design, e só se corrige com reprojeto. Um bug é um erro de implementação. Nasce no código, e se corrige com patch. Essa distinção é o coração do ponto, porque patch nenhum conserta uma falha de design. Por isso o design errado custa uma reunião, e o código errado custa meses.

Quarto elo: os conceitos de base, o que vale em todas as fases. O primeiro é o CIA: confidencialidade, integridade e disponibilidade. Só quem deve acessa; o dado é o que deveria ser; está lá quando se precisa. CIA é o mandato da segurança, o critério contra o qual todo requisito é medido. CIA é fundamento, não é objetivo nem princípio.

Depois do CIA, a postura: assume breach. Presumir que a invasão já aconteceu, e desenhar para conter o dano. É isso que torna dois princípios obrigatórios, e não opcionais. O primeiro, defense in depth: várias barreiras independentes, de modo que a falha de uma não exponha tudo. O atacante precisa vencer todas; o defensor só precisa que uma segure. O segundo, least privilege, o menor privilégio: cada pessoa e cada componente recebe o mínimo de acesso que permite o trabalho, para reduzir o alcance de um erro ou de um comprometimento.

Ainda nos conceitos de base: a ameaça interna. Quem tem acesso legítimo também é vetor, por malícia ou por acidente. Logo o perímetro não basta; os controles precisam valer dentro, para contas autenticadas. Daí o zero trust: never trust, always verify. Todo dado e toda requisição são não confiáveis até prova, inclusive os que vêm da rede interna.

Mais princípios de base: sem segurança por obscuridade, o que é secreto é a chave, não o mecanismo. Sem segredo em código, porque o repositório é clonado e o histórico guarda tudo; segredo fica em cofre ou variável de ambiente. Redução da superfície de ataque: desligar o que não se usa, expor só o necessário. Cadeia de suprimentos: código de terceiros roda com os seus privilégios e toca os seus dados, então exige inventário. E segurança usável: o controle que atrapalha é o controle que o usuário contorna.

Quinto elo: as cinco fases. Em ordem: requisitos, design, código, testes, deploy e manutenção. O mnemônico é R, D, C, T, D.

Fase um, requisitos. O objetivo é levantar os ativos, classificá-los e escrever o requisito de forma verificável. Três princípios: verificabilidade, rastreabilidade e negação explícita. Um requisito ruim diz "seja seguro". Um requisito bom diz "a rota autenticada rejeita token expirado com quatrocentos e um". Sem teste, não entrou no ciclo. As técnicas, com fonte: abuse case, McDermott e Fox, mil novecentos e noventa e nove, que é o atacante externo; misuse case, Sindre e Opdahl, dois mil, que é o insider; attack tree, Schneier, mil novecentos e noventa e nove; e bug bar, do Microsoft SDL.

Fase dois, design. É onde se decide a estrutura, e onde nasce a falha de design. A técnica central é a modelagem de ameaças, de Adam Shostack, dois mil e quatorze, com quatro perguntas: o que estamos construindo; o que pode dar errado; o que fazemos a respeito; e fizemos um bom trabalho. Apoiam o design o diagrama de fluxo de dados, DeMarco, mil novecentos e setenta e nove, e a análise de superfície de ataque, de Manadhata e Wing, dois mil e quatro.

Fase três, código. A técnica-raiz é a inspeção formal de código, de Fagan, mil novecentos e setenta e seis: revisão estruturada, com papéis e checklist, que é a raiz do code review moderno. A regra de ouro: se você está escrevendo criptografia, autenticação ou sessão do zero, pare, porque já existe implementação revisada.

Fase quatro, testes. São sete os alvos: código, aplicação, infraestrutura, banco de dados, APIs, integrações e rede. Esquecer um deixa buraco. As quatro análises: SAST, análise estática, lê o fonte sem executar; DAST, análise dinâmica, ataca a aplicação rodando; SCA, análise de composição, varre as dependências por vulnerabilidades conhecidas; e fuzzing, que joga entrada malformada até algo quebrar. Essas análises aparecem também na fase de código, e não é redundância: no código rodam rápido, por commit, para devolver ao desenvolvedor; nos testes rodam completas, com evidência, para decidir a release.

Fase cinco, deploy e manutenção. A técnica de resposta a incidentes é o NIST oitocentos e sessenta e um: conter, erradicar, recuperar e aprender. A regra de ouro é fazer o fire drill antes do incidente. Três princípios da fase: assume breach, operar como se já estivesse dentro; log sem segredo; e detecção que vale o que vale a resposta, porque alerta que ninguém lê não existe. Dois mecanismos de proteção em runtime: o WAF, firewall de aplicação web, que filtra antes da aplicação; e o RASP, autoproteção em runtime, que protege de dentro. Nenhum dos dois corrige a falha; ambos só compram tempo até o patch.

Sexto e último elo: DevSecOps. Development, security e operations. Segurança deixa de ser tarefa de um time isolado no fim e passa a ser responsabilidade compartilhada pelos três, do começo ao fim. Cada commit dispara verificações automáticas, e a regra de ouro do pipeline é simples: se uma verificação falhar, o pipeline para. Nada vai para produção com falha de segurança conhecida.

Fecho com a fonte-âncora de todo o ponto: Tanya Janca, Alice and Bob Learn Application Security, Wiley, dois mil e vinte.

Recapitulando a corrente, para fixar: custo cresce no tempo; por isso pushing left; falha não é bug; conceitos de base; cinco fases, requisitos, design, código, testes, deploy; e DevSecOps. Se cada elo puxou o próximo de cabeça, o ponto um está internalizado.
