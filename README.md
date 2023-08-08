import re
import sys

def menu():
    print("""OPTIONS:\n
             R - Read PDB file \n
             S - Search PDB file \n
             W - Write out \n
             I - Information \n
             A - Alignment \n
             H - Help \n
             Q - Quit program """)

#Request user for desired option
global option 
option= input("Choose your option:")
menu()

global file
global special_list
special_list=[]

if option.upper() == "R":
    global read
    def read():
        import os.path 
        file=input("Please enter the name of the PDB file: ")
        path="/home/onan/Desktop/PBIO/Semester two/Biopython/MscMak2022-IntroductionToPython/python-miniproject-2022-Onanmulumba/Data/"
        full_path=path+file

        if os.path.exists(full_path) == True:
            print("Successfully loaded valid pdb file:",file)
            special_list.append(full_path)

        else:
            print("File or path '%s'" %(full_path), "does not exist \nFile should be located in the directory: %s" % path)

    read()
    #Function to load more files if required
    def more_files():
        files=input("Would you like to load another file?(Enter yes or no):") 
        if files.lower()=="yes":
            read()
            more_files()

    more_files()

# print(special_list)

elif option.upper()== "S": 
    global search
    def search():
        if len(special_list)==0: #if search option is chosen before uploading files, run the read function
            read()
            more_files() #Upload more than one file if required
            print(special_list)
        else:
            print(special_list)

        count_list=[]
        count=0

        print("The loaded file(s) are:   \n")     
        for i in special_list:      # For loop to output loaded file options     
            print("  OPTION    FILE(s)")      
            print("    ", count ,"   ", i[61:])
            count_list.append(str(count))
            count+=1
        print(count_list)
        global prompt
        prompt=input("\nChoose a number corresponding the file to work with from the options in count_list:")

    # Dictionary with amino acids as one letter codes
        OneCode = {'ALA':'A','CYS':'C','ASP':'D','GLU':'E','PCA':'E','PHE':'F','GLY':'G','HIS':'H','ILE':'I','LYS':'K','LEU':'L','MET':'M',
               'ASN':'N','PRO':'P','GLN':'Q','ARG':'R','SER':'S','THR':'T','VAL':'V','TRP':'W','TYR':'Y','LLP':'K'}

    #Open file you have chosen to work with from prompt
        with open(special_list[int(prompt)],"r") as insulin:
            listA=[]

            for l in insulin:     
                if l.startswith("SEQRES"): #This only pulls out SEQRES and outputs it
                    split_line = l.split()   # Split lines by column    
                    if split_line[2] == "A":  # For each line with an A at index 2(alpha chains),        
                        listA.append(split_line[4:]) # append items from index four(amino acid residues) to listA
        #print(listA)   

            flat_listA = []                     #create new list  
            for sublist in listA:                                                    
                for item in sublist:            #2nd for loop considers each triplet code in each sublist and appends them to flat_listA       
                    flat_listA.append(item)
    #     print(flat_listA)

            Aa_seqA="".join(OneCode[x] for x in flat_listA) #Convert aminoacids in flat_listA to single letter code sequence

            print(Aa_seqA)
    search()

    # Write out function
elif option.upper() == "W": 
    def write_out():
        if len(special_list)==0: #if search option is chosen before uploading files, run the read function
            read()
            more_files() #Upload more than one file if required
            print(special_list)
        else:
            print(special_list)

        count_list=[]
        count=0

        print("The loaded file(s) are:   \n")     
        for i in special_list:      # For loop to output loaded file options     
            print("  OPTION    FILE(s)")      
            print("    ", count ,"   ", i[62:])
            count_list.append(str(count))
            count+=1
        print(count_list)

        File_selection = input("Please select an index number corresponding to the file you want to use")

        print("""OPTIONS\n
        WC = Write out coordinates\n
        WS = Write out sequence""") #Print write out menu option
        
        prompt_1 = input("Enter 'WC' to write out coordinate file ot 'WS' to write out Sequence file ")
        if prompt_1.upper()=="WC": 
            outfile = input("Enter preferred output file name:")
            outfile = outfile + ".txt"
            print(outfile)
            with open("/home/onan/Desktop/PBIO/Semester two/Biopython/MscMak2022-IntroductionToPython/python-miniproject-2022-Onanmulumba/Data/%s" %outfile, "w+"):
                path="/home/onan/Desktop/PBIO/Semester two/Biopython/MscMak2022-IntroductionToPython/python-miniproject-2022-Onanmulumba/Data/"
                WC_output_file = path+outfile
      
            prompt1_1 = input("Please enter A to writeout all coordinate atoms, B to provide to writeout all coordinates for Backbone atoms or C to writeout coordinates for all alpha-carbon")
            if prompt1_1.upper()=="A":
                with open (special_list[int(File_selection)], "r") as insulin:
                    ATOMS_list = []
                    for line in insulin:
                     if line.startswith("ATOM"): 
                        split_line = line.split()
                        ATOMS = split_line[2]
                        ATOMS_list.append(ATOMS)
                    print(str(ATOMS_list))
                    written_file = open(WC_output_file, "w+")
                    for item in ATOMS_list:
                        enumerate(item)
                        written_file.write(str(item))
                        written_file.write("\n")
                    written_file.close
                            #open and read the file after the appending:
                    written_file = open(WC_output_file, "r")
                    print(written_file.read())


            elif prompt1_1.upper()=="B": #For this option, your program should write out coordinates for all backbone atoms                            
                with open (special_list[int(File_selection)], "r") as Backbone:
                    BACKBONEATOMS = ["CA", "C", "N", "O"]
                    BBATOMSCORD_list = []
                    for line in Backbone:
                     if line.startswith("ATOM"): 
                        split_line = line.split()
                        BATOMS = split_line[2]
                        for i in BATOMS:
                            for element in BACKBONEATOMS:
                                if i == element:
                                    coordinates = split_line[6:11]
                                    BBATOMSCORD_list.append(coordinates)
                    import csv
                    written_file = open(WC_output_file, "w+")
                    written_file2 = csv.writer(written_file, delimiter="\t")
                    written_file2.writerows(BBATOMSCORD_list)
                    
                    # for coordinate in BBATOMSCORD_list:
                    #     written_file.write(str(coordinate))
                    #     written_file.write("\n")
                    written_file.close
                            #open and read the file after the appending:
                    # written_file = open(WC_output_file, "r")

            elif prompt1_1.upper()=="C": #Alpha carbon atoms only                             
                with open (special_list[int(File_selection)], "r") as Alphacarbon:
                    # Alpha_carbon_atom = "CA"
                    Alphacarbons_list = []
                    for line in Alphacarbon:
                        if line.startswith("ATOM"): 
                            split_line = line.split()
                            if split_line[2] == "CA":
                            #for i in AC_ATOMS:
                                #if i == Alpha_carbon_atom:
                                coordinates = split_line[6:11]
                                Alphacarbons_list.append(coordinates)
                    import csv
                    written_file = open(WC_output_file, "w+")
                    written_file2 = csv.writer(written_file, delimiter="\t")
                    written_file2.writerows(Alphacarbons_list)
                    
                   
                    written_file.close

        if prompt_1.upper()=="WS": 
            outfile = input("Enter preferred output file name:")
            outfile = outfile + ".txt"
            print(outfile)
            with open("/home/onan/Desktop/PBIO/Semester two/Biopython/MscMak2022-IntroductionToPython/python-miniproject-2022-Onanmulumba/Data/%s" %outfile, "w+"):
                path="/home/onan/Desktop/PBIO/Semester two/Biopython/MscMak2022-IntroductionToPython/python-miniproject-2022-Onanmulumba/Data/"
                WC_output_file = path+outfile
      
            prompt1_2 = input("Please enter 'S' to write the SEQRES Sequence, 'C' to write the Coordinate Sequence, or 'A' to write the Alignment Sequence: ")
            if prompt1_2.upper()=="S": # This writes out the sequence of the PDB file according to the SEQRES section of the PDB file.                            
                with open (special_list[int(File_selection)], "r") as sequence:
                    sequence_list = []
                    for line in sequence:
                        if line.startswith("SEQRES"): 
                            split_line = line.split()
                            codons = split_line[4:]
                            sequence_list.append(codons)

                # Dictionary with amino acids as one letter codes
            OneCode = {'ALA':'A','CYS':'C','ASP':'D','GLU':'E','PCA':'E','PHE':'F','GLY':'G','HIS':'H','ILE':'I','LYS':'K','LEU':'L','MET':'M',
               'ASN':'N','PRO':'P','GLN':'Q','ARG':'R','SER':'S','THR':'T','VAL':'V','TRP':'W','TYR':'Y','LLP':'K'}

            import sys
            sys.stdout= open(WC_output_file, "w+")
            for sublist in sequence_list:
                for item in sublist:

                    Aa_SeqA="".join(OneCode[x] for x in sublist)
    
                    print(">seqID", "\n", Aa_SeqA)
            

                    
                  

    write_out()
                 
