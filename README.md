package rapid.config.data;

import jakarta.annotation.PostConstruct;
import org.springframework.context.annotation.Profile;
import org.springframework.stereotype.Component;
import rapid.model.sysprin.vendor.Vendor;
import rapid.repository.sysprin.VendorRepository;

import java.util.ArrayList;
import java.util.List;

@Component
@Profile("dev")
public class VendorDataGenerator {

    private final VendorRepository vendorRepository;
    public VendorDataGenerator(VendorRepository vendorRepository) {
        this.vendorRepository = vendorRepository;
    }

    private static final String RAW = """
            v40,"Teleservices",1,1,"tafmsas2","","ndmfrd0","0718CF1F9445528370DDFE84CA612E9D","Rapid-Amex.TXT","Transferdata","Rapid","Rapid\\\\Archive",A,O,0,10:30:00,2015/09/07,NULL,NULL,3,NULL,NULL
            v41,"Letters",1,1,"tafmsas2","","ndmfrd0","0718CF1F9445528370DDFE84CA612E9D","AmexLetters.txt","Transferdata","Rapid","Rapid\\\\Archive",A,I,0,10:30:00,2015/09/08,NULL,NULL,3,NULL,NULL
            v42,"Solutions",1,1,"tafmsas2","","ndmfrd0","0718CF1F9445528370DDFE84CA612E9D","Solutions.txt","Transferdata","Temp","Rapid\\\\Archive",A,I,0,10:30:00,2015/09/08,NULL,NULL,3,NULL,NULL
            v43,"Remail Memo Rejects",1,1,"tafmsas2","","ndmfrd0","0718CF1F9445528370DDFE84CA612E9D","FWRreject.OUT","Transferdata","Rapid","Rapid\\\\Archive",R,I,0,10:30:00,2015/09/08,NULL,NULL,3,NULL,NULL
            v44,"Memo Rejects",1,1,"tafmsas2","","ndmfrd0","0718CF1F9445528370DDFE84CA612E9D","RETRreject.OUT","Transferdata","Rapid","Rapid\\\\Archive",R,I,0,10:30:00,2015/09/08,NULL,NULL,3,NULL,NULL
            v45,"Vendor 06",1,0,"tafmsas2","","ndmfrd0","PWD06","V06.TXT","Transferdata","Rapid","Rapid\\\\Archive",A,O,0,11:00:00,2015/09/09,NULL,NULL,3,NULL,NULL
            v46,"Vendor 07",1,0,"tafmsas2","","ndmfrd0","PWD07","V07.TXT","Transferdata","Rapid","Rapid\\\\Archive",A,I,0,11:00:00,2015/09/09,NULL,NULL,3,PASS,A
            v47,"Solutions Rec",1,1,"tafmsas2","","ndmfrd0","0718CF1F9445528370DDFE84CA612E9D","RFIIRA2I.TXT","Transferdata","RAPID\\\\Solutions\\\\Input\\\\RFIIRA2I",NULL,A,I,0,10:30:00,2015/09/08,NULL,"Sol1",3,NULL,NULL
            v48,"Solutions Send",1,1,"tafmsas2","","ndmfrd0","0718CF1F9445528370DDFE84CA612E9D","SOLU.TXT","Transferdata","RAPID\\\\Solutions\\\\Output\\\\RFIIRA2I",NULL,A,I,0,10:30:00,2015/09/08,NULL,NULL,3,NULL,NULL
            v49,"Vendor 10",1,0,"tafmsas2","","ndmfrd0","PWD10","V10.TXT","Transferdata","Rapid","Rapid\\\\Archive",A,O,0,12:00:00,2015/09/10,NULL,NULL,3,NULL,NULL
            v50,"Vendor 11",1,1,"tafmsas2","","ndmfrd0","PWD11","V11.TXT","Transferdata","Rapid","Rapid\\\\Archive",A,I,0,12:00:00,2015/09/10,NULL,NULL,3,PASS,A
            v51,"Vendor 12",1,0,"tafmsas2","","ndmfrd0","PWD12","V12.TXT","Transferdata","Rapid","Rapid\\\\Archive",R,O,0,12:30:00,2015/09/11,NULL,NULL,3,NULL,NULL
            v52,"Vendor 13",1,1,"tafmsas2","","ndmfrd0","PWD13","V13.TXT","Transferdata","Rapid","Rapid\\\\Archive",R,I,0,12:30:00,2015/09/11,NULL,NULL,3,NULL,NULL
            v53,"Vendor 14",1,0,"tafmsas2","","ndmfrd0","PWD14","V14.TXT","Transferdata","Rapid","Rapid\\\\Archive",A,O,0,13:00:00,2015/09/12,NULL,NULL,3,NULL,NULL
            v54,"Vendor 15",1,1,"tafmsas2","","ndmfrd0","PWD15","V15.TXT","Transferdata","Rapid","Rapid\\\\Archive",A,I,0,13:00:00,2015/09/12,NULL,NULL,3,PASS,B
            v55,"Vendor 16",1,0,"tafmsas2","","ndmfrd0","PWD16","V16.TXT","Transferdata","Rapid","Rapid\\\\Archive",R,O,0,13:30:00,2015/09/13,NULL,NULL,3,NULL,NULL
            v56,"Vendor 17",1,1,"tafmsas2","","ndmfrd0","PWD17","V17.TXT","Transferdata","Rapid","Rapid\\\\Archive",R,I,0,13:30:00,2015/09/13,NULL,NULL,3,NULL,NULL
            v18,"Vendor 18",1,0,"tafmsas2","","ndmfrd0","PWD18","V18.TXT","Transferdata","Rapid","Rapid\\\\Archive",A,O,0,14:00:00,2015/09/14,NULL,NULL,3,NULL,NULL
            v19,"Vendor 19",1,1,"tafmsas2","","ndmfrd0","PWD19","V19.TXT","Transferdata","Rapid","Rapid\\\\Archive",A,I,0,14:00:00,2015/09/14,NULL,NULL,3,NULL,NULL
            v20,"Vendor 20",1,0,"tafmsas2","","ndmfrd0","PWD20","V20.TXT","Transferdata","Rapid","Rapid\\\\Archive",R,O,0,14:30:00,2015/09/15,NULL,NULL,3,PASS,A
            v21,"Vendor 21",1,1,"tafmsas2","","ndmfrd0","PWD21","V21.TXT","Transferdata","Rapid","Rapid\\\\Archive",R,I,0,14:30:00,2015/09/15,NULL,NULL,3,NULL,NULL
            v22,"Vendor 22",1,0,"tafmsas2","","ndmfrd0","PWD22","V22.TXT","Transferdata","Rapid","Rapid\\\\Archive",A,O,0,15:00:00,2015/09/16,NULL,NULL,3,NULL,NULL
            v23,"Vendor 23",1,1,"tafmsas2","","ndmfrd0","PWD23","V23.TXT","Transferdata","Rapid","Rapid\\\\Archive",A,I,0,15:00:00,2015/09/16,NULL,NULL,3,NULL,NULL
            v24,"Vendor 24",1,0,"tafmsas2","","ndmfrd0","PWD24","V24.TXT","Transferdata","Rapid","Rapid\\\\Archive",R,O,0,15:30:00,2015/09/17,NULL,NULL,3,NULL,NULL
            v25,"Vendor 25",1,1,"tafmsas2","","ndmfrd0","PWD25","V25.TXT","Transferdata","Rapid","Rapid\\\\Archive",R,I,0,15:30:00,2015/09/17,NULL,NULL,3,NULL,NULL
            v26,"Vendor 26",1,0,"tafmsas2","","ndmfrd0","PWD26","V26.TXT","Transferdata","Rapid","Rapid\\\\Archive",A,O,0,16:00:00,2015/09/18,NULL,NULL,3,NULL,NULL
            v27,"Vendor 27",1,1,"tafmsas2","","ndmfrd0","PWD27","V27.TXT","Transferdata","Rapid","Rapid\\\\Archive",A,I,0,16:00:00,2015/09/18,NULL,NULL,3,PASS,B
            v28,"Vendor 28",1,0,"tafmsas2","","ndmfrd0","PWD28","V28.TXT","Transferdata","Rapid","Rapid\\\\Archive",R,O,0,16:30:00,2015/09/19,NULL,NULL,3,NULL,NULL
            v29,"Vendor 29",1,1,"tafmsas2","","ndmfrd0","PWD29","V29.TXT","Transferdata","Rapid","Rapid\\\\Archive",R,I,0,16:30:00,2015/09/19,NULL,NULL,3,NULL,NULL
            v30,"Vendor 30",1,0,"tafmsas2","","ndmfrd0","PWD30","V30.TXT","Transferdata","Rapid","Rapid\\\\Archive",A,O,0,17:00:00,2015/09/20,NULL,NULL,3,NULL,NULL
            v31,"Vendor 31",1,1,"tafmsas2","","ndmfrd0","PWD31","V31.TXT","Transferdata","Rapid","Rapid\\\\Archive",A,I,0,17:00:00,2015/09/20,NULL,NULL,3,NULL,NULL
            v32,"Vendor 32",1,0,"tafmsas2","","ndmfrd0","PWD32","V32.TXT","Transferdata","Rapid","Rapid\\\\Archive",R,O,0,17:30:00,2015/09/21,NULL,NULL,3,NULL,NULL
            v33,"Vendor 33",1,1,"tafmsas2","","ndmfrd0","PWD33","V33.TXT","Transferdata","Rapid","Rapid\\\\Archive",R,I,0,17:30:00,2015/09/21,NULL,NULL,3,PASS,A
            v34,"Vendor 34",1,0,"tafmsas2","","ndmfrd0","PWD34","V34.TXT","Transferdata","Rapid","Rapid\\\\Archive",A,O,0,18:00:00,2015/09/22,NULL,NULL,3,NULL,NULL
            v35,"Vendor 35",1,1,"tafmsas2","","ndmfrd0","PWD35","V35.TXT","Transferdata","Rapid","Rapid\\\\Archive",A,I,0,18:00:00,2015/09/22,NULL,NULL,3,NULL,NULL
            v36,"Vendor 36",1,0,"tafmsas2","","ndmfrd0","PWD36","V36.TXT","Transferdata","Rapid","Rapid\\\\Archive",R,O,0,18:30:00,2015/09/23,NULL,NULL,3,NULL,NULL
            v37,"Vendor 37",1,1,"tafmsas2","","ndmfrd0","PWD37","V37.TXT","Transferdata","Rapid","Rapid\\\\Archive",R,I,0,18:30:00,2015/09/23,NULL,NULL,3,NULL,NULL
            v38,"Vendor 38",1,0,"tafmsas2","","ndmfrd0","PWD38","V38.TXT","Transferdata","Rapid","Rapid\\\\Archive",A,O,0,19:00:00,2015/09/24,NULL,NULL,3,NULL,NULL
            v39,"Vendor 39",1,1,"tafmsas2","","ndmfrd0","PWD39","V39.TXT","Transferdata","Rapid","Rapid\\\\Archive",A,I,0,19:00:00,2015/09/24,NULL,NULL,3,PASS,B
            """;

    @PostConstruct
    public void seed() {
        if (vendorRepository.count() == 0) {
            vendorRepository.saveAll(parseVendors());
        }
    }

    private List<Vendor> parseVendors() {
        List<Vendor> list = new ArrayList<>();
        for (String line : RAW.strip().split("\\R")) {
            String[] f = line.split(",(?=(?:[^\"]*\"[^\"]*\")*[^\"]*$)", -1);
            int i = 0;
            Vendor v = new Vendor();
            v.setVendId(trim(f[i++]));
            v.setVendName(trim(f[i++]));
            v.setActive(toBool(f[i++]));
            v.setVendReceiverCode(toBool(f[i++]));
            v.setFileServerName(trim(f[i++]));
            v.setFileServerIp(trim(f[i++]));
            v.setFileServerUserId(trim(f[i++]));
            v.setFileServerPassword(trim(f[i++]));
            v.setFileName(trim(f[i++]));
            v.setUncShare(trim(f[i++]));
            v.setPath(trim(f[i++]));
            v.setArchivePath(trim(f[i++]));
            v.setVendorTypeCode(trim(f[i++]));
            v.setFileIo(trim(f[i++]));
            v.setClientSpecific(toBool(f[i++]));
            v.setSchedule(trim(f[i++]));
            v.setDateLastWorked(trim(f[i++]));
            v.setFileSize(toInteger(f[i++]));
            v.setFileTransferSpecs(trim(f[i++]));
            v.setFileType(toInteger(f[i++]));
            v.setFtpPassive(nullIfNullString(f[i++]));
            v.setFtpFileType(nullIfNullString(f[i++]));
            list.add(v);
        }
        return list;
    }

    private static String trim(String s) { return s.replaceAll("^\"|\"$", "").trim(); }

    private static Boolean toBool(String s) {
        String t = trim(s);
        if (t.isBlank() || t.equalsIgnoreCase("NULL")) return null;
        return t.equals("1") || t.equalsIgnoreCase("Y") || t.equalsIgnoreCase("T");
    }

    private static Integer toInteger(String s) {
        String t = trim(s);
        return t.isBlank() || t.equalsIgnoreCase("NULL") ? null : Integer.valueOf(t);
    }

    private static String nullIfNullString(String s) {
        String t = trim(s);
        return t.isBlank() || t.equalsIgnoreCase("NULL") ? null : t;
    }
}
