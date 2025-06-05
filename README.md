Deleted_Cases:

case_number	char	NO	12
pi_id	char	YES	16
customer_id	char	YES	24
primary_pi_id	char	YES	16
account	char	YES	16
last_name	char	YES	20
first_name	char	YES	16
hm_phone	char	YES	14
wk_phone	char	YES	14
entity_cd	char	YES	1
role_cd	char	YES	2
pi_status	char	YES	1
status	char	YES	1
active	bit	NO	NULL
reason	char	YES	1
subreason	int	YES	NULL
disposition	char	YES	1
in_hour	int	YES	NULL
in_date	datetime	YES	NULL
next_date	datetime	YES	NULL
out_date	datetime	YES	NULL
auto_date	datetime	YES	NULL
num_cards	int	YES	NULL
final_action_cards_nr	int	YES	NULL
delivery_id	int	YES	NULL
sys_prin	char	YES	8
cycle	char	YES	1
Frst_Updt_Vend_id	char	YES	3
Contact_cd	char	YES	1
Contact_Ph_nr	char	YES	18
Return_Reason_cd	char	YES	2
Issuance_cd	char	YES	1
issuance_dt	datetime	YES	NULL
WorkStation_name_tx	char	YES	18
Operator_CD	char	YES	2
Barcode_Type_CD	char	YES	1
rec_typ_tx	char	YES	2
srvc_typ_tx	char	YES	3
mailer_id	char	YES	9
AS400_client_id	char	YES	4
AS400_system_id	char	YES	8
bsc_spplmntl_id	char	YES	1
orig_ml_dt	datetime	YES	NULL
msg_id	char	YES	40
ml_mthd	char	YES	30
source_file	char	YES	16
cust_id	char	YES	9
ms_issue_date	char	YES	5
cust_id2	char	YES	9
mkt_cd	char	YES	2
account_tokenid	char	YES	16
pi_id_tokenid	char	YES	16
primary_pi_id_tokenid	char	YES	16

robot_labels

user_name	char	YES	25
reporting_date	smalldatetime	YES	NULL
bundles	int	YES	NULL
individual_labels	int	YES	NULL
robot_label_total	int	YES	NULL


labels

text1	varchar	YES	50
text2	varchar	YES	50
text3	varchar	YES	50
text4	varchar	YES	50
text5	varchar	YES	50
status	bit	NO	NULL
Type	varchar	YES	10
case_number	char	YES	16
card_type_tx	char	NO	7
Bar_Code_tx	varchar	YES	70
text3_Addr3	char	YES	50
text3_Addr4	char	YES	50









 QueueManager=;Server=odsmq-qao-oma.1dc.com(1414);Channel=RAPIDODS.SVRCONN;RequestQueue=RAPIDODS.RQST.OCYCLE.QUEUE;ReplyQueue=RAPIDODS.REPLY.MQAJ.QUEUE;User=dsfraud;Password=;




 import javax.jms.Connection;
import javax.jms.ConnectionFactory;
import javax.jms.JMSException;
import org.springframework.stereotype.Service;

@Service
public class MqConnectionTestService {

    private final ConnectionFactory connectionFactory;

    public MqConnectionTestService(ConnectionFactory connectionFactory) {
        this.connectionFactory = connectionFactory;
    }

    public void testConnection() {
        try (Connection connection = connectionFactory.createConnection()) {
            connection.start();  // Start the connection to MQ
            System.out.println("✅ Successfully connected to IBM MQ!");
        } catch (JMSException e) {
            System.err.println("❌ Failed to connect to IBM MQ: " + e.getMessage());
            e.printStackTrace();
        }
    }
}







Error:  Failed to execute goal on project admin: Could not resolve dependencies for project admin:admin:jar:1.0.0-SNAPSHOT: The following artifacts could not be resolved: org.apache.tomcat.embed:tomcat-embed-core:jar:10.1.36 (absent): Could not transfer artifact org.apache.tomcat.embed:tomcat-embed-core:jar:10.1.36 from/to Nexus (https://nexus-dev.onefiserv.net/repository/mvn-gl-flume-public-group/): status code: 403, reason phrase: -------------------->>> REQUESTED ITEM IS QUARANTINED -------------------->>> FOR DETAILS SEE ------>>> https://sonatype.fiserv.one/ui/links/malware-defense/repositories/quarantinedComponent/ZGU4NThiYzg3ZTA2NGFhMmI2OWJkZGJmYjk0Y2RmNTI <<<------ (



















