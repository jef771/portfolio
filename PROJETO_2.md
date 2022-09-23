<p align="center"> <img src="img/logo_poc.png" class="center" width=150/> </p>
<h2 align="center">
POC - Processos Otimizados de Contas
</h2>

Trabalhei no projeto da API com o Parceiro Acadêmico TecSUS.<br>
A empresa produz soluções tecnológicas para transmissão e recepção de dados, controle, gestão de faturas e até mesmo equipamentos remotos voltados para os setores de abastecimento de água, distribuição de eletricidade e gás natural.<br>
Visto que a gestão de faturas pode ser algo complicado pela falta de padrão das empresas envolvidas no setor a TecSUS precisava de uma ferramenta para digitalizar um grande volume de contas.<br>
O projeto, Desktop App, foi feito totalmente em Java e foi focado em ajudar o processo de digitação de contas que não seguiam o padrão do sistema de digitalização automática de contas.<br>
Esse, sem dúvida, foi o projeto mais bem coordenado, todos os professores estavam em sintonia, assim sendo um projeto tranquilo para a implementação.<br>
[link para GIT](https://github.com/MikeBBatista/pi-fatec-java)

#### Tecnologias Utilizadas
Java, MySQL, IDE Eclipse Java

#### Contribuições Pessoais
Fui responsável pelo DAO - Objeto ou classe de acesso a dados, ele que conectava ao banco e fazia as transações -, como a intenção do trabalho era usar Java puro, trabalhei muito com o POO (programação orientada a objetos), lógica de programação, criação de query e JDBC para chegar até as soluções dos problemas.

###### - <i>DAO</i> e <i>Controller</i>
- A seguir é possível clicar e ler alguns trechos de código, foi a primeira vez que tive contato com arquitetura MVC - <i>Model</i>, <i>Controller</i> e <i>View</i>-, são exemplos concisos de tudo de novo que vi no projeto, camada DAO - conexão entre <i>backend</i> e banco de dados-, e comunicação com o frontend o <i>Controller</i>.
<br>
<br>

Uma inserção no banco de dados da conta de luz.
<details>
  <summary markdown="span">DAO</summary>

  ```java
  public class LightAccountDao {
	
	public static Integer save(LightAccount light) {
		
		Integer result = 0;
		String sql = "Insert into LIGHT_ACCOUNT (LIGHT_IDENT_COD, "
				+ "LIGHT_METER_NUMBER, "
				+ "LIGHT_INVOICE, "
				+ "LIGHT_CURRENT_DATE, "
				+ "LIGHT_DUE_DATE, "
				+ "LIGHT_CONSUMPTION_DAYS, "
				+ "LIGHT_FLAG_TYPE, "
				+ "LIGHT_CONSUMPTION_VALUE, "
				+ "LIGHT_PIS_PERCENTAGE, "
				+ "LIGHT_COFINS_PERCENTAGE, "
				+ "LIGHT_ICMS_BASIS, "
				+ "LIGHT_ICMS_PERCENTAGE, "
				+ "LIGHT_ICMS_VALUE, "
				+ "LIGHT_PIS_COFINS_BASIS, "
				+ "LIGHT_PIS_VALUE, "
				+ "LIGHT_COFINS_VALUE, "
				+ "LIGHT_FORFEIT_VALUE, "
				+ "LIGHT_INTEREST_VALUE, "
				+ "LIGHT_OTHER_VALUES, "
				+ "LIGHT_SUPPLY_VALUES, "
				+ "LIGHT_FINANCIAL_ITEMS, "
				+ "LIGHT_AMOUNT, "
				+ "LIGHT_SUPPLIER_CNPJ,"
				+ "LIGHT_USER_ID,"
				+ "LIGHT_ALTER_BY) "
				+ "VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?)";
		
		try {
			BaseConnection con = new BaseConnection();
			PreparedStatement saveValues = con.connection.prepareStatement(sql);
			
			saveValues.setInt(1, light.getIdentCod());
			saveValues.setInt(2, light.getMeterNumber());
			saveValues.setString(3, light.getInvoice());
			saveValues.setString(4, light.getCurrentDate());
			saveValues.setString(5, light.getDueDate());
			saveValues.setInt(6, light.getConsumptionDays());
			saveValues.setString(7, light.getFlagType());
			saveValues.setBigDecimal(8, light.getConsumptionValue());
			saveValues.setBigDecimal(9, light.getPisPercentage());
			saveValues.setBigDecimal(10, light.getCofinsPercentage());
			saveValues.setBigDecimal(11, light.getIcmsBasis());
			saveValues.setBigDecimal(12, light.getIcmsPercentage());
			saveValues.setBigDecimal(13, light.getIcmsValue());
			saveValues.setBigDecimal(14, light.getPisCofinsBasis());
			saveValues.setBigDecimal(15, light.getPisValue());
			saveValues.setBigDecimal(16, light.getCofinsValue());
			saveValues.setBigDecimal(17, light.getForfeitValue());
			saveValues.setBigDecimal(18, light.getInterestValue());
			saveValues.setBigDecimal(19, light.getOtherValues());
			saveValues.setBigDecimal(20, light.getSupplyValue());
			saveValues.setBigDecimal(21, light.getFinancialItems());
			saveValues.setBigDecimal(22, light.getAmount());
			saveValues.setLong(23, light.getSupplierCnpj());
			saveValues.setInt(24, light.getCreatedBy());
			saveValues.setInt(25, light.getAlterBy());
			
			result = saveValues.executeUpdate();
		}
		catch(SQLException err) {
			System.out.println(err);
		}
		
		return result;
	}		
 ```
</details>
<br>
<br>

Comunicação entre o front com o <i>DAO</i>.
<details>
  <summary markdown="span">Controller</summary>

  ```java
  public class LightAccountController {
	
	public static void saveValues(Integer identCod, Integer meterNumber, String invoice, String currentDate,
			String dueDate, Integer consumptionDays, String flagType, BigDecimal consumptionValue, BigDecimal pisPercentage,
			BigDecimal cofinsPercentage, BigDecimal icmsBasis, BigDecimal icmsPercentage, BigDecimal icmsValue,
			BigDecimal pisCofinsBasis, BigDecimal pisValue, BigDecimal cofinsValue, BigDecimal forfeitValue,
			BigDecimal interestValue, BigDecimal otherValues, BigDecimal supplyValue, BigDecimal financialItems,
			BigDecimal amount, Long supplierCnpj, Integer createdBy, Integer alterBy) {
		
		LightAccount light = new LightAccount(identCod, meterNumber, invoice, currentDate, dueDate, consumptionDays,
				flagType, consumptionValue, pisPercentage, cofinsPercentage, icmsBasis, icmsPercentage, icmsValue,
				pisCofinsBasis, pisValue, cofinsValue, forfeitValue, interestValue, otherValues, supplyValue,
				financialItems, amount, supplierCnpj, createdBy, alterBy);
		
		
		if(LightAccountDao.save(light) == 1) {
			
			showMessageDialog(null, "Dados cadastrados com Sucesso!");
		}
	}
			public static List <LightAccount> getValues (String identCod){
				List <LightAccount> lightAccounts = LightAccountDao.listLightAccounts(identCod);
				return lightAccounts;
			}
		
			public static void updateValues (LightAccount lightaccount) {
				if (LightAccountDao.update(lightaccount) == 1) {
					showMessageDialog(null, "Dados alterados com sucesso!");
				}
				else {
					showMessageDialog(null,"Dados do tipo incorreto, verifique e tente novamente");
				}
			}
			
 		}	
 ```
</details>
	


#### Hard Skills Efetivamente Desenvolvidas
- [x] <b>Java</b>
    - Nesse projeto, que um dos requisitos dos professores era a utilização do Java puro, pude treinar o uso de pacotes do jdk como utils e lang.
    - Sei fazer com autonomia
- [x] POO
    - Trabalhei com programação orientada a objetos o encapsulamento foi muito utilizado nesse projeto, encapsulando cada camada em seu determinado pacote
    - Sei fazer com autonomia
- [x] <b>JDBC</b>
    - Para comunicação entre <b>backend</b> e banco de dados.
    - Sei fazer com ajuda
- [x] <b>SQL</b>
    - Foi preciso escrever cada query!
    - Sei fazer com ajuda

#### Soft Skills Efetivamente Desenvolvidas
- O começo desse projeto foi conturbado, precisei <b>tomar a decisão</b> de sair do meu primeiro devido a grande carga de trabalho que eu estava lidando. Foi uma boa decisão já que no segundo grupo <b>trabalhei</b> muito bem em <b>equipe</b> e acabei ajudando os membros da minha equipe de forma decisiva, principalmente na parte de lógica de programação, mas também precisei da ajuda deles! Precisei entregar menos cards logo na última <i>sprint</i> devido a problemas pessoais, uma boa <b>comunicação</b> entre todos foi importante para manter uma boa relação entre todos do grupo.
