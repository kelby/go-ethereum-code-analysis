initGenesis

os.Open\(genesisPath\)

genesis := new\(core.Genesis\)

stack, \_ := makeConfigNode\(ctx\)

stack.OpenDatabase

core.SetupGenesisBlock

chaindb.Close

方法：SetupGenesisBlock

SetupGenesisBlockWithOverride

