<p align="center"> <img src="img/logo_airplan.jpg" class="center" width=150/> </p>
<h2 align="center">
AirPlan
</h2>

Trabalhei no projeto da API com o Parceiro Acadêmico Embraer.<br>
A Embraer é uma empresa renomada e importantíssima para o Brasil, produzindo aeronaves que são exportadas para dezenas de países e também utilizadas em solo brasileiro. Com o grande número de aeronaves produzidas, a construção e manutenção da documentação da aeronave - que pode chegar a milhares de páginas - torna-se algo complexo, portanto procuravam uma solução digital e moderna para a varredura e montagem de PDFs e leitura automatizada de Excel.<br>

[link para GIT](https://github.com/GabrielSG20/Projeto_Integrador_3BD-1Sem2021)

#### Tecnologia Utilizadas
Java SE 8, MySQL, IDE Eclipse, GitHub, IText, Maven e Framework Springboot

#### Contribuições Pessoais
Fui responsável pela maioria do <b>backend</b>, pela arquitetura MVC, regras de serviço e "raspagem" de dados.

###### - Regras de Serviço
- O maior desafio do projeto. A lógica de programação para executar a raspagem de dados dos manuais em formato pdf, como ainda estávamos sendo introduzidos a programação mais avançada (que lida com estruturas de dados) precisei estudar em todo tempo livre que tive para aprender novas estruturas e saber onde aplica-las também usei muito a biblioteca externa <i>itextpdf</i>.

###### - Arquitetura do Sistema (MVC)
<img src="img/arquitetura-mvc.png">
- É possível verificar exemplos de Controller (onde é feita a comunicação entre <i>front</i> e <i>back</i>), Repository (camada de comunicação entre <i>back</i> e banco de dados) e Service (camada de lógica da aplicação, no caso: raspagem de dados).
<br>
<br>
<i>Controller</i> da LEP, parte do sistema que recebia dados via excel
<details>
  <summary markdown="span">Controller</summary>
	
```java
@Controller
public class LEPController {

    private final LepService lepService;

    public LEPController(LepService lepService) {
        this.lepService = lepService;
    }

    @RequestMapping("/lep-create")
    public String showLepCreatePage(Model model) {
        Lep lep = new Lep();
        model.addAttribute("lep", lep);


        return "lep-create";
    }

    @PostMapping(value = "/lep-create")
    public String createLep(@ModelAttribute("lep") Lep lep,
                            RedirectAttributes redirAttrs) throws IOException {


        if(!lepService.checkIntegrity(lep)) {
            redirAttrs.addFlashAttribute("error", "Incorrect data, check" +
                    " fields integrity, eg.: all fields are filled?");
            return "redirect:/lep-create";
        }

        lepService.createLep1(lep);
        redirAttrs.addFlashAttribute("success", "LEP successfully created!");

        return "redirect:/lep-create";
    }
}
```
</details>
<br>
<br>
Esse <i>repository</i> faz a conexão entre o <i>backend</i> e banco de dados, procurando manual pelo nome e checando quantos manuais existem com o nome informado
<details>
  <summary markdown="span">Repository</summary>
	
```java
@Repository
public interface ManualRepository extends JpaRepository<Manual, Integer> {
    @Query(" select mnl_id from Manual where mnl_name = ?1 ")
    Integer findManualByName(String nomeManual);
    @Query("select count(mnl_id) from Manual where mnl_name = ?1")
    Long checkCount(String nomeManual);
}
```
</details>
<br>
<br>
O <i>service</i> da principal funcionalidade do projeto, raspagem de dados dos arquivos pdfs dos manuais.
<details>
  <summary markdown="span">Service (raspagem de dados!)</summary>
	
```java
@Service
public class LepService
{
    private final ManualService manualService;
    private final CodeListService codeListService;
    private String currentRevision;

    @Autowired
    public LepService(ManualService manualService, CodeListService codeListService) {
        this.manualService = manualService;
        this.codeListService = codeListService;
        this.currentRevision = "";
    }

    public boolean checkIntegrity(Lep lep) {

        if(lep.getRevision_dates().length() == 0) {
            return false;
        } else if(lep.getCdl_code().length() == 0){
            return false;
        } else if(lep.getMnl_name().length() == 0) {
            return false;
        } else return lep.getFlg_tag().length() != 0;
    }

    public void createLep1(final Lep lep) throws IOException {
        List<String> filterRevisionDates = new ArrayList<>();
        final String[] rvParts = lep.getRevision_dates().split("-");
        for (int i = 0; i < rvParts.length; ++i) {
            StringBuilder rvFinal = new StringBuilder();
            for (int j = 0; j < rvParts[i].length(); ++j) {
                rvFinal.append(rvParts[i].charAt(j));
                if (j == 7 || j == 8) {
                    rvFinal.append(".................");
                }
                if(i == rvParts.length-1 && j <= 8) {
                    if(Character.isDigit(rvParts[i].charAt(j))) {
                        if(Character.isDigit(rvParts[i].charAt(j+1))) {
                            currentRevision+= " ";
                        } else {
                            currentRevision+=" 0";
                        }
                    }
                    currentRevision+=rvParts[i].charAt(j);
                }
            }
            filterRevisionDates.add(rvFinal.toString());
        }

        final Integer mnl_id = this.manualService.findManualByName(lep.getMnl_name());
        this.createLep2(lep, mnl_id, filterRevisionDates);
    }

    public void addCell(String block, String code, Integer page,
                        String change, Table table, Integer lastPage) {

        if (page == 1) {
            table.addCell(new Cell().add(block).setBorder(Border.NO_BORDER).setTextAlignment(TextAlignment.CENTER)
                    .setBorderTop(new SolidBorder(0.5f)));
            table.addCell(new Cell().add(code).setBorder(Border.NO_BORDER).setTextAlignment(TextAlignment.CENTER)
                    .setBorderTop(new SolidBorder(0.5f)));
            table.addCell(new Cell().add(String.valueOf(page)).setBorder(Border.NO_BORDER).setTextAlignment(TextAlignment.CENTER)
                    .setBorderTop(new SolidBorder(0.5f)));
            if (change.equals(currentRevision)) {
                table.addCell(new Cell().add("*").setBorder(Border.NO_BORDER)
                        .setBorderTop(new SolidBorder(0.5f)));
            } else {
                table.addCell(new Cell().add("").setBorder(Border.NO_BORDER).setTextAlignment(TextAlignment.CENTER)
                        .setBorderTop(new SolidBorder(0.5f)));
            }
            table.addCell(new Cell().add(change).setBorder(Border.NO_BORDER).setTextAlignment(TextAlignment.CENTER)
                    .setBorderTop(new SolidBorder(0.5f)));

        } else if (page == lastPage) {
            table.addCell(new Cell().add(block).setBorder(Border.NO_BORDER).setTextAlignment(TextAlignment.CENTER)
                    .setBorderBottom(new SolidBorder(0.5f)));
            table.addCell(new Cell().add(code).setBorder(Border.NO_BORDER).setTextAlignment(TextAlignment.CENTER)
                    .setBorderBottom(new SolidBorder(0.5f)));
            table.addCell(new Cell().add(String.valueOf(page)).setBorder(Border.NO_BORDER).setTextAlignment(TextAlignment.CENTER)
                    .setBorderBottom(new SolidBorder(0.5f)));
            if (change.equals(currentRevision)) {
                table.addCell(new Cell().add("*").setBorder(Border.NO_BORDER)
                        .setBorderBottom(new SolidBorder(0.5f)));
            } else {
                table.addCell(new Cell().add("").setBorder(Border.NO_BORDER).setTextAlignment(TextAlignment.CENTER)
                        .setBorderBottom(new SolidBorder(0.5f)));
            }
            table.addCell(new Cell().add(change).setBorder(Border.NO_BORDER).setTextAlignment(TextAlignment.CENTER)
                    .setBorderBottom(new SolidBorder(0.5f)));
        } else {
            table.addCell(new Cell().add(block).setBorder(Border.NO_BORDER)).setTextAlignment(TextAlignment.CENTER);
            table.addCell(new Cell().add(code).setBorder(Border.NO_BORDER)).setTextAlignment(TextAlignment.CENTER);
            table.addCell(new Cell().add(String.valueOf(page)).setBorder(Border.NO_BORDER)).setTextAlignment(TextAlignment.CENTER);
            if (change.equals(currentRevision)) {
                table.addCell(new Cell().add("*").setBorder(Border.NO_BORDER));
            }
            else {
                table.addCell(new Cell().add("").setBorder(Border.NO_BORDER)).setTextAlignment(TextAlignment.CENTER);
            }
            table.addCell(new Cell().add(change).setBorder(Border.NO_BORDER)).setTextAlignment(TextAlignment.CENTER);
        }
    }
						  
   ...

}
    
```
</details>


###### - Grande Contribuição
- Esse foi um dos projetos que mais contribui e de maneira essencial para o sucesso do grupo.
<br>
<img src="img/contribuicoes.png">
<br>

#### Hard Skills Efetivamente Desenvolvidas
- [x] <b>JPA</b>
    - Primeiro contato com essa api extremamente utilizada no mercado de trabalho Java, abstrai a comunicação entre <i>back</i> e banco de dados de forma intuitiva.
    - Sei fazer com autonomia
- [x] Uso de arquitetura <b>MVC</b>
    - Como o projeto foi mudando ao longo das sprints á arquitetura MVC ajudou muito para integração do código e adição de novas <i>features</i>
    - Sei fazer com autonomia
- [x] Uso de bibliotecas externas como IText
    - O pronto focal do projeto era a "raspagem" de dados dos manuais portanto o uso dessa api externa foi essêncial para a finalização do projeto, assim percebi que como desenvolvedor não preciso criar tudo do zero, posso aprender a utilizar apis e ferramentas já sólidas na comunidade.
    - Sei fazer com ajuda
- [x] Implementação de exceptions
    - Algo extremamente importante para tornar-se um bom programador.
    - Sei fazer com autonomia
- [x] <b>Spring boot</b>
    - Framework completo e muito utilizado no mercado de trabalho Java, de fácil configuração e facilidade de boot já que usa servidor acoplado.].
    - Sei fazer com autonomia
	
#### Soft Skills
Esse projeto teve uma complexidade muito alta, principalmente para o momento do curso, porém, tendo a <b>resiliência</b> adquirida em outros projetos consegui, com <b>autonomia</b>, estudar e aprender o que era necessário para desenvolver o projeto. Porém nem sempre força de vontade resolve, precisei ser <b>criativo</b> para resolver algumas questões da implementação e contei com a ajuda dos professores em questões de hard skills e soft skills <b>comunicando</b> minhas dificuldades em certos aspectos do projeto. 
Outro ponto interessante foi na raspagem de dados, como nem sempre o usuário seguia um padrão de digitação foi minha <b>responsabilidade</b> achar esse padrão inusitado, foi necessária uma boa dose de <b>paciência</b> para encontrá-lo.
