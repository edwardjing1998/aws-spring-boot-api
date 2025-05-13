public class CommandLineParser {

    public static void main(String[] args) {
        String commandLine = "EXEC FRPC 'CNONMON','4470431112096227','698','00',,'BLL1','F',,,,,,,'4543 KNOLLCROFT ROAD',' ',,,'TROTWOOD','OH','USA','45426-1936',,,,,'P',,,,,'U'";
        String sNonMon = extractNonMon(commandLine);
        System.out.println("sNonMon: " + sNonMon); // Expected Output: "00"
    }

    public static String extractNonMon(String commandLine) {
        int iPosition;
        int iNext;
        String sNonMon = "";

        // Check if "CNONMON'" exists in the command line
        if (commandLine.contains("CNONMON'")) {
            iPosition = commandLine.indexOf("CNONMON'");

            // Move iPosition 12 characters ahead (to get past "CNONMON'")
            iPosition = iPosition + 12;

            // Find the position of the next "',"
            iNext = commandLine.indexOf("','", iPosition);

            // Ensure iNext is valid
            if (iNext > iPosition) {
                // Move iNext to the start of the non-monetary value
                iNext = iNext + 3; // past "',"

                // Extract 2 characters starting at this position
                if (iNext + 2 <= commandLine.length()) {
                    sNonMon = commandLine.substring(iNext, iNext + 2);

                    // Check if the last character of sNonMon is a "'" and remove it if so
                    if (sNonMon.endsWith("'")) {
                        sNonMon = sNonMon.substring(0, 1);
                    }
                }
            }
        }

        return sNonMon;
    }
}
