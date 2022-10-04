<p align="center"> <img src="img/logo_endurance.png" class="center" width=200/> </p>
<h2 align="center">
Endurance
</h2>

Trabalhei no projeto da API com o Parceiro Acadêmio Oracle.<br>
A Oracle é uma empresa renomada e importatíssima para o mercado trazendo sempre com novas soluções para banco de dados, seja em IA ou nuvem. Essas soluções precisam ser discutidas e apresentadas, por conseguinte, a Oracle Brasil possui um espaço especial para esse proposito, a Casa Oracle.<br>
Com grande demanda, a empresa precisou de um software para gestão de reuniões, participantes e palestrantes.<br>
Esse projeto foi caracterizado pela simplicidade, porém muito marcante para o mercado de trabalho, pois pedia soluções utilizadas no mercado tecnicologico.<br>
[link para GIT](https://github.com/MaXximiles/API-4SEM)


#### Tecnologia Utilizadas
Java SE 14, GitHub, Framework Springboot, Oracle Autonomous Database , Angular e Maven.

#### Contribuições Pessoais
Fiquei encarregado de todo o <i>backend</i> do projeto e de parte da criação do banco de dados. A arquitetura que eu escolhi foi a MVC (<i>Model</i> <i>View</i> <i>Controller</i>), pois, além de adequar-se muito bem às soluções propostoas, ainda é muito utilizada no mercado de trabalho, pela sua facilidade de prototipação, dessa forma trazendo um desenvolvimento mais simples e um conhecimento de Hard Skill útil. Também optei pelo padrão <i>facade</i> ou seja, o cliente faz requisições (em JSON) para o programa portanto o <b>Spring Boot</b> também foi o mais adequado.
###### - Arquitetura do Sistema
- Uma visão geral da arquitetura do programa. Já que na parte <i>View</i> foi utilizado um <i>framework</i> de <i>frontend</i> (Angular) o <i>backend</i> ficou encarregado da parte <i>Model</i> e <i>Controller</i> e outros pacotes interessantes para o projeto como <i>exception</i> para um melhor controle do fluxo do programa e <i>constant</i> para deixar o código mais legível.
<br>
<img src="img/MVC.png">
<br>

###### - <i>Backend</i>
- Fiz todo o backend do projeto, controller, model, service e repository, porém o que eu mais desenvolvi foi a qualidade do código.
- Abaixo é possível clicar e visualizar um exemplo de uma das 3 entidades do código fonte. Podemos ver a utilização da biblioteca <b>Lombok</b> para simplificar e manter o código mais legível eliminando código <i>boilerplate</i> (código recorrente como <i>getters</i> e <i>setters</i>). Também podemos observar a utilização do <i>framework</i> <b>Hibernate</b> sendo utilizado no seu modelo <b>JPA</b> para abstrair e deixar mais simples a comunicação entre o banco de dados e a camada <i>Model</i>. Também temos exemplos de diversos tipos de mapeamento de entidades.
<details>
	
###### - <i>Entity</i>
- Um exemplo de uma <i>entity</i>, classe para mapear a tabela do banco de dados. Como trabalhei com Spring Boot utilizei anotações para fazer o mapeamento, utilizando o JPA para a comunicação com o banco de dados.
<summary markdown="span"y>Entidade</summary>

```java
@Entity
@NoArgsConstructor
@AllArgsConstructor
@Getter
@Setter
@Builder
@ToString
@Table(
        name = Evento.TABLE_NAME,
        uniqueConstraints = @UniqueConstraint(
                name = "evt_tema_unique",
                columnNames = "evt_tema"
        )

)
public class Evento implements Serializable {

    public static final String TABLE_NAME = "EVENTOS";
    public static final String ID_NAME = "EVT_ID";
    public static final String SEQUENCE_NAME = "EVENTOS_SEQUENCE";
    public static final String COLUNA_INICIO = "EVT_INICIO";
    public static final String COLUNA_FIM = "EVT_FIM";
    public static final String COLUNA_LOCAL = "EVT_LOCAL";
    public static final String COLUNA_TEMA = "EVT_TEMA";
    public static final String COLUNA_DESCRICAO = "EVT_DESCRICAO";
    public static final String COLUNA_OBSERVACAO = "EVT_OBSERVACAO";
    public static final String COLUNA_USUARIO = "EVT_USR_ID";
    public static final String COLUNA_CRIACAO = "EVT_CRIACAO";
    public static final String COLUNA_STATUS = "EVT_STATUS";
    public static final String COLUNA_MAX_PARTICIPANTES = "EVT_MAX_PART";
    public static final String COLUNA_TOTAL_PARTICIPANTES = "EVT_TOTAL_PART";


    @Id
    @SequenceGenerator(
            name = SEQUENCE_NAME,
            sequenceName = SEQUENCE_NAME,
            allocationSize = 1
    )
    @GeneratedValue(
            strategy = GenerationType.IDENTITY,
            generator = SEQUENCE_NAME
    )
    @Column(name=ID_NAME, nullable = false)
    private Long id;
    @Column(name=COLUNA_INICIO, nullable = false)
    @JsonFormat(pattern = "yyyy-MM-dd'T'HH:mm:ss")
    private LocalDateTime inicio;
    @Column(name=COLUNA_FIM, nullable = false)
    @JsonFormat(pattern = "yyyy-MM-dd'T'HH:mm:ss")
    private LocalDateTime fim;
    @NotBlank()
    @Column(name=COLUNA_LOCAL, columnDefinition = "VARCHAR2(9)", nullable = false)
    private String local;
    @NotBlank()
    @Column(name=COLUNA_TEMA, columnDefinition = "VARCHAR2(50)", nullable = false, unique = true)
    private String tema;
    @Column(name=COLUNA_DESCRICAO, columnDefinition = "VARCHAR2(150)")
    private String descricao;
    @Column(name=COLUNA_OBSERVACAO, columnDefinition = "VARCHAR2(150)")
    private String observacao;
    @OneToOne
    @JoinColumn(
            name = COLUNA_USUARIO,
            referencedColumnName = User.ID_NAME,
            nullable = false
    )
    private User user;
    @Column(name=COLUNA_CRIACAO, nullable = false)
    @NotBlank
    private LocalDateTime criacao = LocalDateTime.now();
    @Column(name=COLUNA_STATUS, columnDefinition = "VARCHAR2(10)", nullable = false)
    private String status;
    @Column(name=COLUNA_MAX_PARTICIPANTES, nullable = false)
    private Integer maxParticipantes;
    @Column(name=COLUNA_TOTAL_PARTICIPANTES, nullable = false)
    private Integer totalParticipantes;

    @ManyToMany
    @JoinTable(
            name="evento_usuario_part",
            joinColumns = @JoinColumn(
                    name = "eup_evt_id",
                    referencedColumnName = ID_NAME
            ),
            inverseJoinColumns = @JoinColumn(
                    name = "eup_usr_id",
                    referencedColumnName = User.ID_NAME
            )
    )
    private List<User> participantes;

    @ManyToMany
    @JoinTable(
            name="evento_fornecedor_map",
            joinColumns = @JoinColumn(
                    name = "efm_evt_id",
                    referencedColumnName = "evt_id"
            ),
            inverseJoinColumns = @JoinColumn(
                    name="efm_frn_id",
                    referencedColumnName = "frn_id"
            )
    )
    private List<Fornecedor> fornecedores;

    public boolean addParticipante(User user) {
        if(this.maxParticipantes > this.totalParticipantes) {
            participantes.add(user);
            this.maxParticipantes++;
            this.totalParticipantes++;
            return true;
        }
        return false;
    }
}
```
</details>

###### - <i>Unit Test</i>
- Um exemplo de um dos testes feitos para a aplicação, foi a minha primeira experiência com testes unitários e eles me ajudaram para manter a qualidade do software, além de facilitar o trabalho.
- Testes para verificar se já existe um evento no horário do novo evento sendo adicionado ou se existe um evento ocorrendo no horário do novo evento
	
<details>
<summary markdown="span">Unit Test</summary>
	
```java
@SpringBootTest
class EventoServiceImplTest {

    @MockBean
    private EventoRepository eventoRepository;

    @Autowired
    private EventoService underTest;

    private User user;

    @BeforeEach
    void setUp() {
        this.user = User
                .builder()
                .firstName("Teste")
                .lastName("S")
                .email("teste@gmail.com")
                .cpf("973.017.940-96")
                .joinDate(new Date())
                .password("123")
                .isActive(true)
                .isNotLocked(false)
                .role(ROLE_GUEST.name())
                .authorities(ROLE_GUEST.getAuthorities())
                .profileImageUrl(null)
                .id(1L)
                .build();
    }

    @Test
    @DisplayName("Add Evento com início no meio de outro Evento == Exc")
    void whenEventoOccurring_ShouldThrowExc()  {

        LocalTime open = LocalTime.of(10,00,00);
        LocalDateTime date = LocalDateTime.of(LocalDateTime.now().toLocalDate(), open);
        System.out.println(date);

        // given
        Evento event = Evento
                .builder()
                .id(1L)
                .inicio(date)
                .fim(date.plusHours(2L))
                .local(LocalEvento.OPENSPACE.name())
                .tema("Lean Agile")
                .descricao("Entenda a nova tendência de arquitetura de software")
                .observacao("Necessário carteira de vacinação")
                .user(user)
                .criacao(LocalDateTime.now())
                .status(StatusEvento.PENDENTE.name())
                .maxParticipantes(50)
                .totalParticipantes(1)
                .build();

        Evento event2 = Evento
                .builder()
                .id(2L)
                .inicio(date.plusMinutes(30L))
                .fim(date.plusHours(3L))
                .local(LocalEvento.OPENSPACE.name())
                .tema("Lean Agile 2")
                .descricao("Entenda a nova tendência de arquitetura de software 2")
                .observacao("Necessário carteira de vacinação 2")
                .user(this.user)
                .criacao(LocalDateTime.now())
                .status(StatusEvento.PENDENTE.name())
                .maxParticipantes(50)
                .totalParticipantes(1)
                .build();

        List<Evento> events = List.of(event);
        LocalDate date2 = event2.getInicio().toLocalDate();
        given(eventoRepository.findEventoByDate(date2))
                .willReturn(java.util.Optional.of(events));

        //when

        Throwable exc = assertThrows(EventIsOccurringException.class, () -> underTest.addEvento(event2));

        // then
        assertEquals("Evento ocorrendo no horário de início: "
                + event2.getInicio().toLocalTime().format(DateTimeFormatter.ofPattern("HH:mm"))
                + ". Sugestão de horário:08:00",
                exc.getMessage());


    }

    @Test
    @DisplayName("Add Evento com início == de outro Evento == Exc")
    void whenEventoExist_ShouldThrowExc() {

        // given
        LocalTime open = LocalTime.of(10,00,00);
        LocalDateTime date = LocalDateTime.of(LocalDateTime.now().toLocalDate(), open);

        Evento event = Evento
                .builder()
                .id(1L)
                .inicio(date)
                .fim(date.plusHours(1L))
                .local(LocalEvento.OPENSPACE.name())
                .tema("Lean Agile")
                .descricao("Entenda a nova tendência de arquitetura de software")
                .observacao("Necessário carteira de vacinação")
                .user(this.user)
                .criacao(LocalDateTime.now())
                .status(StatusEvento.PENDENTE.name())
                .maxParticipantes(50)
                .totalParticipantes(1)
                .build();

        Evento event2 = Evento
                .builder()
                .id(2L)
                .inicio(date)
                .fim(date.plusHours(3L))
                .local(LocalEvento.OPENSPACE.name())
                .tema("Lean Agile 2")
                .descricao("Entenda a nova tendência de arquitetura de software 2")
                .observacao("Necessário carteira de vacinação 2")
                .user(this.user)
                .criacao(LocalDateTime.now())
                .status(StatusEvento.PENDENTE.name())
                .maxParticipantes(50)
                .totalParticipantes(1)
                .build();

        List<Evento> events = List.of(event);
        LocalDate date2 = event2.getInicio().toLocalDate();
        given(eventoRepository.findEventoByDate(date2))
                .willReturn(java.util.Optional.of(events));

        //when

        Throwable exc = assertThrows(EventoInicioExistException.class, () -> underTest.addEvento(event2));

        // then
        assertEquals("Evento já cadastrado com ínício: "
                        + event2.getInicio().toLocalTime().format(DateTimeFormatter.ofPattern("HH:mm"))
                + ". Sugestão de horário:08:00",
                exc.getMessage());


    }

   ...
{
```
</details>

#### Hard Skills Efetivamente Desenvolvidas
- [x] <b>JPA</b>
    - Extremamente utilizado no mercado de trabalho Java, eu já conhecia algumas anotações e parametros mas me aprofundei muito mais nesse projeto
    - Sei ensinar
- [x] Uso de constantes para deixar o código mais legível
    - Como os demais integrantes do grupo precisam ler meu código para fazer a comunicação com o <i>frontend</i> decidi dar mais atenção a essa parte e acabou sendo de grande ajuda até para mim, por exemplo: foi muito mais fácil de fazer alterações
    - Sei fazer com autonomia
- [x] <b>Spring security</b>
    - Consegui fazer uma boa introdução a essa biblioteca muito utilizada no mercado de trabalho
    - Sei fazer com ajuda
- [x] Banco de Dados em Cloud
    - Foi um desafio sair da zona de conforto de só baixar a instalar um banco de dados local mas valeu muito a pena
    - Sei fazer com ajuda
- [x] Uso de bibliotecas externas como <b>OAuth 2.0</b> e <b>StringUtils</b>
    - Sei fazer com ajuda
- [x] Implementação de exceptions
    - Algo extremamente importante para tornar-se um bom programador
    - Sei ensinar
- [x] Comunicação entre <i>frontend</i> e <i>backend</i> por JSON
    - No começo do projeto participei muito do <i>frontend</i> e foi muito legal aprender sobre requisições HTML e JSON
    - Sei fazer com autonomia
- [x] Envio de emails
    - Finalmente descobri como funciona!
    - Sei fazer com ajuda
- [x] Teste Unitário
    - Sei fazer com ajuda

#### Soft Skills
- [x] Organização<br>
    - Precisei de muita! Seja na vida pessoal ou no código - para gerenciar toda a programação do <b>backend</b>.
- [x] Trabalho em Equipe<br>
    - Como o Frontend e Backend foram separados nessa api, foi necessário muito trabalho em equipe para os dois funcionarem de maneira correta.
- [x] Empatia<br>
    - Alguns membros do time tiveram vários problemas durante o semestre então precisei entender os seus motivos e executar alguns trabalhos fora do meu escopo.
