#!/usr/bin/python3

# an itty bitty script to transpose columns and rows of a file 

from os import listdir
import argparse
import pandas as pd

parser = argparse.ArgumentParser(description='Transposes rows to columns in a text file.')
parser.add_argument('--filename', help='filename input')
parser.add_argument('--shellFilename', help='filename for the shell script', default="out.sh")
parser.add_argument('--debug', help='print debug output', action="store_true")
args = parser.parse_args()

# read the file names 
poly  = pd.read_csv(args.filename, sep = "\t", header = None) 



# transpose rows and columns
print("Transposing the data set")
df = poly.transpose()
df.to_csv(args.shellFilename, sep = "\t", header = None) 

# close the file 

