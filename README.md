Create an inter thread communication program of printer job by implementing two threads, one for calculating the remaining pages in printer tray and other one will print the pages that are pending on queue. (Hint: If total pages are 10 and user sends job for 15 pages than print thread will be on wait and will be notified once available pages are equal or greater than printing pages).
    
    class PrinterTray {
    int pages = 10;
    synchronized void printPages(int req) {
        System.out.println("PrintThread: Requested to print " + req + " pages");

        if (pages < req) {
            System.out.println("PrintThread: Not enough pages. Available = " + pages);
            System.out.println("PrintThread: Waiting for more pages...");
            try { wait(); } catch (Exception e) {}
        }
        System.out.println("PrintThread: Notification received");
        System.out.println("PrintThread: Enough pages available. Printing " + req + " pages...");
        pages -= req;
        System.out.println("PrintThread: Printing completed");
        System.out.println("PrintThread: Remaining pages in tray = " + pages);
    }
    synchronized void addPages(int newPages) {
        System.out.println("\nTrayThread: Checking tray pages...");
        pages += newPages;
        System.out.println("TrayThread: Added " + newPages + " pages to tray");
        System.out.println("TrayThread: Total pages now = " + pages);
        System.out.println("TrayThread: Notifying PrintThread...");
        notify();
    }
    }
    class PrintThread extends Thread {
    PrinterTray tray;

    PrintThread(PrinterTray tray) {
        this.tray = tray;
    }

    public void run() {
        tray.printPages(15);
    }
    }
    class TrayThread extends Thread {
    PrinterTray tray;

    TrayThread(PrinterTray tray) {
        this.tray = tray;
    }

    public void run() {
        try { Thread.sleep(2000); } catch (Exception e) {}
        tray.addPages(10);
    }
    }
    public class Wasay2 {
    public static void main(String[] args) {

        PrinterTray tray = new PrinterTray();

        PrintThread pt = new PrintThread(tray);
        TrayThread tt = new TrayThread(tray);

        pt.start();
        tt.start();
    }
}
