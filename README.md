# desafio-aws-cloud-formation

# AWS CloudFormation
**Este repositório documenta anotações e insights técnicos adquiridos durante a aula sobre AWS CloudFormation.**

**Objetivos: 🎯**
* Documentar processos técnicos de forma clara e estruturada.
* Aplicar os conceitos aprendidos em um ambiente prático (hands-on).
* Servir como material de apoio para estudos e futuras implementações.

**Conceitos: 💡**

O **AWS CloudFormation** é o principal serviço de Infraestrutura como Código (IaC) da AWS, que nos auxilia na automação e criação de recursos na AWS por meio de templates **JSON** ou **YAML**.

Ele nos ajuda a modelar e configurar nossos recursos da AWS para despendermos menos tempo gerenciando esses recursos e mais tempo nos concentrando em nossos aplicativos executados na AWS.

Criamos um modelo que descreve todos os recursos da AWS desejados, e o CloudFormation cuida do provisionamento e da configuração desses recursos. O CloudFormation está disponível por meio do console, da API, da AWS CLI, dos AWS SDKs e de várias integrações.

**Cenários de Aplicação (Por que usar o CloudFormation): 📊**

**1. Replicar Infraestrutura com Rapidez**
Para garantir alta disponibilidade, uma aplicação pode ser replicada em várias regiões. O desafio é que essa replicação manual exige provisionar e configurar todos os recursos em cada nova região, um processo lento e sujeito a erros.
Com o CloudFormation, podemos reutilizar o mesmo template para criar os recursos de modo consistente e repetível. Basta descrevê-los uma única vez no modelo e, em seguida, provisionar essa infraestrutura rapidamente em múltiplas regiões.

**2. Simplificar o Gerenciamento de Infraestrutura**
Imagine uma aplicação web escalável que utiliza Auto Scaling, Elastic Load Balancing e um banco de dados Amazon RDS. Configurar cada um desses serviços individualmente para que funcionem em conjunto adiciona complexidade e tempo ao processo.
Com o CloudFormation, é possível descrever todos esses recursos e suas dependências em um único template. Ao executar este template, o CloudFormation provisiona todos os serviços como uma unidade coesa, chamada de **Stack** (pilha). Quando a infraestrutura não é mais necessária, a exclusão da *stack* remove automaticamente todos os recursos associados. Dessa forma, é possível gerenciar um conjunto complexo de recursos como se fosse uma única entidade, simplificando todo o seu ciclo de vida.

**3. Controlar e Rastrear Alterações na Infraestrutura**
Ao provisionar a infraestrutura com o CloudFormation, o modelo descreve exatamente quais recursos são provisionados e quais suas configurações.
Como esses modelos são arquivos de texto (JSON/YAML), eles podem ser gerenciados em um sistema de controle de versão (como Git). Isso permite saber exatamente quais alterações foram feitas, quem as fez e quando.
Se ocorrer algum problema após uma atualização, por exemplo, é possível rastrear as diferenças nos modelos para identificar a alteração na infraestrutura. Caso seja necessário, pode-se reverter para uma versão anterior e estável, garantindo uma recuperação rápida e confiável: o famoso **Rollback**.

**Como Funciona: Modelos (Templates) do CloudFormation**

Um modelo do AWS CloudFormation define os recursos da AWS que você deseja criar, atualizar ou excluir como parte de uma pilha. Ele consiste em várias seções, mas a única seção **obrigatória** é a `Resources`, que deve declarar pelo menos um recurso.

**Métodos de Criação de Modelos:**

* **AWS Infrastructure Composer:** Uma interface visual para criar modelos.
* **Editor de texto:** Escreva modelos diretamente na sintaxe JSON ou YAML.
* **Gerador de IaC (IaC Generator):** Gere modelos com base em recursos já provisionados em sua conta que (ainda) não são gerenciados pelo CloudFormation.

**Armazenamento dos Modelos**

Os modelos do CloudFormation são armazenados em um bucket do Amazon S3. Ao criar ou atualizar uma pilha, é possível especificar o URL do modelo no S3 em vez de carregá-lo diretamente. Se o upload do modelo for feito pelo AWS Management Console ou AWS CLI, um bucket do S3 será criado automaticamente para armazená-lo.

**Validação dos Modelos**

* **Validação de Sintaxe:** É possível verificar a sintaxe JSON ou YAML de um modelo usando o comando `validate-template` da CLI ou especificando o modelo no console (que faz a validação automaticamente). Esses métodos apenas verificam a sintaxe, não os valores das propriedades.
* **Validação de Práticas Recomendadas:** Para validações complexas, utilizam-se ferramentas como:
    * **CloudFormation Linter (cfn-lint):** Valida os modelos em relação aos esquemas do provedor de recursos, verificando se foram usados valores válidos para propriedades e se seguem as boas práticas.
    * **CloudFormation Rain (rain fmt):** Formata os modelos em um padrão consistente ou os converte entre JSON e YAML, preservando comentários e otimizando o uso de funções.

**Fluxo de Criação de Modelos**

Para começar a criar um modelo do CloudFormation, siga estas etapas:

1.  **Escolher os recursos:** Identificar quais recursos da AWS serão incluídos na pilha (ex: instâncias EC2, VPCs, Security Groups).
2.  **Escrever o modelo:** Escrever o modelo em formato JSON ou YAML, definindo os recursos e suas propriedades.
3.  **Salvar o modelo:** Salvar o modelo localmente com uma extensão de arquivo apropriada (ex: `.json`, `.yaml`, `.yml` ou `.txt`).
4.  **Validar o modelo:** Utilizar ferramentas como `cfn-lint` ou `rain fmt` para validação completa, ou o comando `validate-template` da CLI para verificação de sintaxe.
5.  **Criar uma pilha:** Criar uma pilha (stack) usando o modelo validado.

**Referências Importantes do Modelo**

Modelos de pilha frequentemente exigem funções e atributos especiais para lidar com valores dinâmicos (disponíveis apenas em tempo de execução) ou para controlar o comportamento dos recursos.

**Funções Intrínsecas (Exemplos Comuns):**

* `Ref`: Recupera o valor de um parâmetro ou o ID físico de um recurso.
* `Fn::Sub`: Substitui espaços reservados (variáveis) em strings por valores reais.
* `Fn::GetAtt`: Retorna o valor de um atributo específico de um recurso no modelo (ex: o DNS de um Load Balancer).
* `Fn::Join`: Une um conjunto de valores em uma única string, usando um delimitador.

**Atributos de Recurso (Exemplos Comuns):**

* `DependsOn`: Especifica que um recurso só deve ser criado após outro recurso ter sido concluído.
* `DeletionPolicy`: Especifica como o CloudFormation deve lidar com um recurso quando a pilha é excluída (ex: `Retain` para manter um banco de dados ou `Snapshot` para criar um backup).
