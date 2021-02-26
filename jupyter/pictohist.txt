#!/usr/bin/python3

import pandas as pd
import numpy as np
from PIL import Image
import sys, os

def black_white(pic):
    thresh = 230
    fn = lambda x : x > thresh and 255
    return pic.convert('L').point(fn, mode='1')

print(sys.argv[1:])

if len(sys.argv) == 2:
    for infile in sys.argv[1:]:
        outfile = os.path.splitext(infile)[0] + "_bw"
        if infile != outfile:
            try:
                with Image.open(infile) as im:
                    bw = black_white(im)
                    binary = np.array(bw,dtype=int)
                    df = pd.DataFrame(binary)
                    df.sum(axis=0).to_csv(outfile+'.csv',header=['hist'])
            except OSError:
                    print("Cannot create histogram for", infile)

elif len(sys.argv) > 2:
    print("Return concatenated histograms? [y,n]")
    inp = input()
    if inp == "y":
        df_list = []
        for infile in sys.argv[1:]:
            try:
                with Image.open(infile) as im:
                    bw = black_white(im)
                    binary = np.array(bw,dtype=int)
                    df = pd.DataFrame(binary)
                    df_list.append(df.sum(axis=0))
            except OSError:
                    print("Cannot create histogram for", infile)

        pd.concat(df_list,axis=1).to_csv('joinedHist' + '.csv',
            header=[f'hist{a}' for a in range(len(sys.argv)-1)])
    elif inp == "n":
        for infile in sys.argv[1:]:
            outfile = os.path.splitext(infile)[0] + "_bw"
            if infile != outfile:
                try:
                    with Image.open(infile) as im:
                        bw = black_white(im)
                        binary = np.array(bw,dtype=int)
                        df = pd.DataFrame(binary)
                        df.sum(axis=0).to_csv(outfile+'.csv',header=['hist'])
                except OSError:
                        print("Cannot create histogram for", infile)

    else: print('Wrong input. Rype y or n ...')
