# Estrangulamento de aplicações - Strangler Pattern

Suponha que você tenha uma aplicação web monolítica legada, construída com uma arquitetura antiga e tecnologias obsoletas. A aplicação possui várias funcionalidades e é difícil de manter e evoluir. Em vez de reconstruir tudo do zero, você decide usar o padrão de estrangulamento para modernizá-la gradualmente.

Passo 1: Identifique uma funcionalidade para modernizar
Comece selecionando uma funcionalidade específica da aplicação que deseja modernizar. Pode ser um módulo, uma página ou um conjunto de funcionalidades relacionadas.

Passo 2: Crie uma nova implementação
Desenvolva uma nova implementação da funcionalidade selecionada usando tecnologias modernas e uma arquitetura mais escalável. A nova implementação pode ser uma aplicação separada, um serviço independente ou uma API.

Passo 3: Roteie o tráfego para a nova implementação
Configure uma camada de roteamento para direcionar parte do tráfego da aplicação legada para a nova implementação da funcionalidade modernizada. Isso pode ser feito por meio de configurações no servidor de aplicação ou por um serviço de roteamento dedicado.

Passo 4: Gradualmente substitua a funcionalidade antiga
À medida que o tráfego é direcionado para a nova implementação, você pode começar a substituir partes da funcionalidade antiga pela nova. Por exemplo, você pode iniciar substituindo alguns componentes de interface do usuário ou lógica de negócios.

Passo 5: Repita o processo para outras funcionalidades
Após modernizar uma funcionalidade com sucesso, você pode repetir o processo para outras partes da aplicação, selecionando novas funcionalidades para modernizar gradualmente. Ao longo do tempo, você substituirá gradualmente a aplicação legada por uma nova arquitetura moderna.

O padrão de estrangulamento permite modernizar a aplicação de forma incremental, minimizando o risco e os impactos na operação em produção. Ele também permite que você aproveite os benefícios de tecnologias e arquiteturas mais recentes sem a necessidade de reescrever todo o código existente.

# O estrangulamento de aplicações monolíticas usando estratégias de arquitetura, como Anti Corruption Layers (ACLs)
Identifique a área a ser modernizada: Comece selecionando uma área específica da aplicação monolítica que você deseja modernizar. Pode ser um módulo, um conjunto de funcionalidades ou uma camada específica da aplicação.

Introduza uma camada de anti corrupção (ACL): Crie uma camada intermediária entre a parte antiga da aplicação e a parte modernizada, chamada de Anti Corruption Layer (ACL). Essa camada atua como um isolamento para proteger a parte modernizada de ser corrompida pela arquitetura antiga.

Defina interfaces limpas: No ACL, defina interfaces limpas e independentes da aplicação legada. Essas interfaces refletem os requisitos da parte modernizada e não devem ser influenciadas pela arquitetura obsoleta.

Realize a transformação gradual: Implemente a parte modernizada da aplicação, seguindo as melhores práticas de design e usando tecnologias modernas. À medida que você desenvolve as novas funcionalidades, interaja com a parte legada da aplicação através do ACL, usando as interfaces limpas definidas anteriormente.

Converta gradualmente: À medida que novas funcionalidades são desenvolvidas e testadas na parte modernizada, você pode gradualmente converter partes da aplicação legada para usar a nova arquitetura, passando a utilizar o ACL para se comunicar com a parte modernizada.

Refatore e repita: Ao longo do tempo, continue refatorando e convertendo partes da aplicação legada, usando o ACL como uma camada de isolamento. Repita o processo até que toda a aplicação seja modernizada.

A camada de Anti Corruption Layer atua como uma fronteira entre a parte modernizada e a parte antiga da aplicação. Ela permite que você modernize partes da aplicação sem ser influenciado pelos padrões arquiteturais legados, mantendo um design limpo e escalável na parte modernizada.

É importante planejar cuidadosamente a arquitetura do ACL, definir interfaces claras e tratar adequadamente os problemas de compatibilidade entre as partes legada e modernizada da aplicação. O ACL também pode incluir lógica de tradução de dados entre os sistemas antigo e moderno, garantindo uma integração adequada.

O uso de ACLs é uma abordagem poderosa para estrangular aplicações monolíticas, permitindo uma modernização gradual e controlada, ao mesmo tempo em que protege a parte modernizada de ser corrompida pela arquitetura legada.

```csharp
// Camada de Aplicação Modernizada

namespace MinhaAplicacao.Modernizada.Services
{
    public class PedidoService
    {
        private readonly IPedidoRepository _pedidoRepository;
        private readonly IACLService _aclService;

        public PedidoService(IPedidoRepository pedidoRepository, IACLService aclService)
        {
            _pedidoRepository = pedidoRepository;
            _aclService = aclService;
        }

        public void CriarPedido(int clienteId, List<ItemPedido> itens)
        {
            // Verificar se o cliente possui permissão para criar um pedido
            if (!_aclService.VerificarPermissao(clienteId, "criar_pedido"))
            {
                throw new InvalidOperationException("O cliente não possui permissão para criar pedidos.");
            }

            // Criação do pedido
            var pedido = new Pedido
            {
                ClienteId = clienteId,
                Itens = itens
            };

            // Salvar pedido na camada modernizada
            _pedidoRepository.Salvar(pedido);
        }

        // ... Outros métodos da aplicação modernizada ...
    }
}

// Camada de ACL

namespace MinhaAplicacao.ACL.Services
{
    public class ACLService : IACLService
    {
        private readonly IACLRepository _aclRepository;

        public ACLService(IACLRepository aclRepository)
        {
            _aclRepository = aclRepository;
        }

        public bool VerificarPermissao(int clienteId, string operacao)
        {
            // Verificar permissão do cliente usando a camada de ACL
            var permissao = _aclRepository.ObterPermissao(clienteId, operacao);

            return permissao != null && permissao.Autorizado;
        }
    }
}

// Camada de Infraestrutura da Aplicação Legada

namespace MinhaAplicacao.Legada.Repositories
{
    public class ACLRepository : IACLRepository
    {
        public Permissao ObterPermissao(int clienteId, string operacao)
        {
            // Implementação para obter permissão do cliente na aplicação legada
            // ...
        }
    }
}
```

Neste exemplo, temos uma aplicação modernizada que utiliza a estratégia de Anti Corruption Layer (ACL) para proteger as partes modernizadas da aplicação de serem corrompidas pela arquitetura antiga. A camada de ACL é responsável por verificar as permissões do cliente, enquanto a camada de aplicação modernizada é responsável pelas funcionalidades mais recentes.

A classe PedidoService na camada de aplicação modernizada utiliza a interface IACLService para verificar se um cliente possui permissão para criar um pedido antes de prosseguir com a operação. A implementação concreta dessa interface está na classe ACLService, que por sua vez utiliza a interface IACLRepository para obter as permissões do cliente. Na camada de infraestrutura da aplicação legada, temos a implementação concreta da interface IACLRepository, que se comunica com a aplicação legada para obter as permissões.

Essa estrutura permite que a aplicação modernizada utilize as funcionalidades mais recentes sem ser afetada pela arquitetura antiga. A camada de ACL serve como uma barreira de proteção, garantindo que as partes modernizadas da aplicação sejam isoladas da arquitetura legada.

## Livro
![Imagem](https://m.media-amazon.com/images/I/61aFldsgAmL._SY425_.jpg)
