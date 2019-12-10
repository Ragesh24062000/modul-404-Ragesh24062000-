/*
 * To change this license header, choose License Headers in Project Properties.
 * To change this template file, choose Tools | Templates
 * and open the template in the editor.
 */
package rezeptfunktionen;

import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.io.Serializable;
import java.util.Collection;
import java.util.Iterator;
import java.util.TreeSet;

/**
 *
 * @author rages
 */
public class Rezepte implements Serializable {

    protected Collection<ReceiptItem> rcpList = new TreeSet<ReceiptItem>();
    protected int letzteID = 0;

    public void addRezept() {
        letzteID++;
        rcpList.add(new ReceiptItem(letzteID));

        System.out.println("Datensatz mit der Nummer " + letzteID + " erfasst.");
    }

    public void listRezepte() {
        for (int i = 0; i < 50; ++i) {
            System.out.println();
        }

        for (Iterator i = this.rcpList.iterator(); i.hasNext();) {
            System.out.println(i.next());
        }
    }

    public void deleteRezept() {
        int id;
        id = selectRezept();
        this.delRezept(id);
    }

    public void editRezept() {
        int id;
        id = selectRezept();
        this.updateRezept(id);
    }

    protected int selectRezept() {
        this.listRezepte();
        System.out.println("Welches Rezept soll geändert/gelöscht werden?");
        return CLesen.readInt();
    }

    protected void updateRezept(int id) {
        ReceiptItem edit = null;
        for (Iterator i = this.rcpList.iterator(); i.hasNext();) {
            ReceiptItem item = (ReceiptItem) i.next();
            if (item.id == id) {
                edit = item;
                break;
            }
        }

        if (edit != null) {
            edit.Edit();
            this.rcpList.remove(edit);
            rcpList.add(edit);
        }
    }

    public static void safeRezepte(Rezepte rcp) {

        try {
            String name = "rezepte.fst";
            FileOutputStream fos = new FileOutputStream(name);
            ObjectOutputStream oos = new ObjectOutputStream(fos);

            oos.writeObject(rcp);
            oos.close();

        } catch (Exception e) {
            System.out.println("Fehler beim Schreiben: " + e);
        }
    }

    public static Rezepte readRezepte() {
        try {
            String name = "rezepte.fst";
            FileInputStream fis = new FileInputStream(name);
            ObjectInputStream ois = new ObjectInputStream(fis);

            Rezepte rcp = (Rezepte) ois.readObject();

            ois.close();
            System.out.println(rcp.letzteID);
            return rcp;
        } catch (Exception e) {
            System.out.println("Fehler beim Lesen: " + e);
        }
        return new Rezepte();
    }

    protected void delRezept(int id) {
        ReceiptItem del = null;
        for (Iterator i = this.rcpList.iterator(); i.hasNext();) {
            ReceiptItem item = (ReceiptItem) i.next();
            if (item.id == id) {
                del = item;
                break;
            }
        }

        if (del != null) {
            this.rcpList.remove(del);
            listRezepte();
        }
    }

    private class ReceiptItem implements Comparable<ReceiptItem>, Serializable {

        protected int id;
        protected String kuchen;
        protected String anzahlPers;
        protected String menge;
        protected String zutaten;

        ReceiptItem(int id, String kuchen, String anzahlPers, String menge, String zutaten) {
            this.id = id;
            this.kuchen = kuchen;
            this.anzahlPers = anzahlPers;
            this.menge = menge;
            this.zutaten = zutaten;
        }

        ReceiptItem(int id) {
            this.id = id;

            System.out.println("Bitte gib die Receiptinfos ein: ");
            System.out.println("Kuchen: ");
            this.kuchen = CLesen.readString();
            System.out.println("Anzahl Personen: ");
            this.anzahlPers = CLesen.readString();
            System.out.println("Menge: ");
            this.menge = CLesen.readString();
            System.out.println("Zutaten: ");
            this.zutaten = CLesen.readString();
        }

        public void Edit() {
            String wert;
            System.out.println("Bitte gib die Receiptinfos ein: ");

            System.out.println("Kuchen: (" + this.kuchen + ") ");

            wert = CLesen.readString();
            this.kuchen = wert.equals("") ? this.kuchen : wert;

            System.out.println("Anzahl Personen: (" + this.anzahlPers + ") ");
            wert = CLesen.readString();
            this.anzahlPers = wert.equals("") ? this.anzahlPers : wert;

            System.out.println("Menge: (" + this.menge + ") ");
            wert = CLesen.readString();
            this.menge = wert.equals("") ? this.menge : wert;

            System.out.println("Zutagen: (" + this.zutaten + ") ");
            wert = CLesen.readString();
            this.zutaten = wert.equals("") ? this.zutaten : wert;
        }

        @Override
        public String toString() {
            return id + "\t" + String.format("%1$-30s", kuchen) + "\t" + String.format("%1$-30s", anzahlPers) + "\t" + String.format("%1$-30s", menge) + "\t" + String.format("%1$-30s", zutaten);
        }

        @Override
        public int compareTo(ReceiptItem other) {
            return Integer.compare(id, other.id);
        }
    }

}
